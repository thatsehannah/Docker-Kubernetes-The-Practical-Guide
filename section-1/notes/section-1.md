- What is Docker?
  - A tool for creating and managing containers

- What is a container?
  - A standardized unit of software
  - A package of code and dependencies to run that code
  - The same contaienr always yields the exact same application and execution behavior, no matter where or by whom it might be executed

- Why Containers?
  - Why would we want independent, standardized "application packages"
    - The development environment and production environment are often not the same. Containers allow us to set up both environments in the exact same way to ensure that our application works exactly as test.
    - The development environment for developer A may differ from the development environment for developer B. Containers allow for both developers to have the same environment when working on the same project.
    - Project A may have different tools/versions than Project B, which could clash. Contaienrs eliminate the clasing between the two projects when switching between the two.
  - We want reliable and reproducible environments
    - We want to have the exact same environment for development and production -> this ensures that it works exactly as expected and tested.
    - It should be easy to share a common development environment with (new) employees and colleagues
    - We don't want to uninstall and re-install local dependencies and runtimes all the time. 

- Virtual Machines/Virtual OS
  | Pros | Cons |
  | ---- | ---- |
  | Separated environments | Redundant duplication, waste of space |
  | Environment-specific configurations are possible | Performance of host machine can be slow |
  | Environment configurations can be shared and reproduced reliably | Reproducing on another computer/server is tricky because you will have to set up that virtual machine in the exact same way, no single configuration file |

- Containers vs Virtual Machines
  - Docker containers
    - Low impact on OS, very fast, minimal disk space usage
    - Sharing, re-building and distribution is easy
    - Encapsulate apps/environments instead of whole machines
  - Virtual machines
    - Bigger impact on OS, slower, higher disk space usage
    - Sharing, re-building and distribution can be challenging
    - Encapsulate "whole machines" instead of just apps/environment

