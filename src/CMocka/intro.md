# Introduction
- CMocka is a framework for testing C software

## Standard includes
- CMocka needs some includes to work

```C
#include <stdarg.h>
#include <stdbool.h>
#include <stddef.h>
#include <stdint.h>
#include <setjmp.h>

#include <cmocka.h>
```

## Asserts
- heres the full list of assert macros: [Assert Macros](https://api.cmocka.org/group__cmocka__asserts.html)

## Test Structure
- declare a const struct CMUnitTest to encapsulate all the tests
- call cmocka_run_group_tests([TEST STRUCT], NULL, NULL) to run tests

- unit test can have some variations
    - with prestate
    - with setup/teardown

- global setup/teardown functions can be set in the 2nd and 3rd parameters of the cmocka_run_group_tests() function

```C
void Test(void **state);

const struct CMUnitTest tests[] = {
    cmocka_unit_test(Test),
    cmocka_unit_test_setup(Test, setup)
    cmocka_unit_test_teardown(Test, teardown)
    cmocka_unit_test_setup_teardown(Test, setup, teardown)
    cmocka_unit_test_prestate(Test, state)
    cmocka_unit_test_prestate_setup_teardown(Test, setup, teardown, state)
};


int main(void)
{
    return cmocka_run_group_tests(tests, NULL, NULL);
}
```

### Prestates
- states can be passed to tests
- useful to iterate over many states of a single test

```C
    typedef struct{
        uint32_t input_a;
        uint32_t input_b;
        uint32_t expected;
    }test_state_t;

    static uint32_t calculate_mdc(uint32_t a, uint32_t b){
        while(b != 0){
            uint32_t temp = a;
            a = b;
            b = temp % b;
        }

        return a;
    }

    static void test_mdc(){
        test_state_t *s = *state;

        assert_uint equal(calculate_mdc(s->input_a, s->input_b), s->expected);
    }

    int main(void){
        test_state_t states[] = {
            {2,  3,  6},
            {4,  6,  12},
            {6,  8,  24},
            {12, 18, 36},
        };

        const struct CMUnitTest tests[] = {
            cmocka_unit_test_prestate(test_mdc, &states[0]),
            cmocka_unit_test_prestate(test_mdc, &states[1]),
            cmocka_unit_test_prestate(test_mdc, &states[2]),
            cmocka_unit_test_prestate(test_mdc, &states[3]),
        };

        return cmocka_run_group_tests(tests, NULL, NULL);
    }
```

