# Patterns

## 1. Constructing through a static method
- makes the constructor of a class private and create an API for constructing said class
- advantage: makes the contruction process more customizable and controlled

```Cpp
// process.hpp
class process{
    public:
        /*disable constructors*/
        process() = delete;
        process(const process&) = delete;
        process& operator=(const process&) = delete;

        /*calls private constructor*/
        static std::unique_ptr<process> launch(std::filesystem::path path);

    private:
        pid_t pid_ = 0;
        bool terminate_on_end_ = true;

        /*private constructor*/
        process(pid_t pid, bool terminate_on_end) : 
            pid_(pid), terminate_on_end_(terminate_on_end) {}
};
```
- it can be used to call constructors of an inherited class as well

```Cpp
    class error : public std::runtime_error{
    public:    
        /*different calls for same inherited constructor*/s
        [[noreturn]]
        static void send(const std::string& what) {throw error(what);}
        
        [[noreturn]]
        static void send_errno(const std::string& prefix){
            throw error(prefix + ": " + std::strerror(errno));
        }

    private:
        /*calls runtime_error class's constructor*/
        error(const std::string& what) : std::runtime_error(what){}
    };
```