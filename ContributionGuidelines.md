# Contribution Guidelines: Adding a new simulation method

If you have an open-source room acoustic simulation method you would like to add to the CHORAS back-end, you have come to the right place! If you simply want to use CHORAS as a front-end for your room acoustics simulation back-end, please read on. On the other hand, if you want your software to become part of the main CHORAS repository, please inform the lead developer (currently Silvin Willemsen: s.willemsen@tue.nl) of your intentions, so we can help you best :)


## Requirements for the room acoustics simulation back-end
Before you follow the steps in this document, please read the requirements below to see if your simulation software fits CHORAS.

- The back-end should simulate **room acoustics** and should only consider enclosed spaces. Simulation software for domains, such as environmental acoustics, are unfortunately are not within the scope of CHORAS.
- The software must take the following **inputs**:
    - Complex geometry (anything more complicated than shoebox)
    - (at least) 1 source location
    - (at least) 1 receiver location
    - Material absorption properties

## Git business
1. Clone this repository.
2. Follow the steps in the README.md for local installation. 
    - Please create a branch of the `backend` submodule (sim/<your_method_acronym>) so that you can work on this freely. Also, if you want to authenticate via ssh (instead of the default https), navigate to the `backend` submodule and run the following command to change the url:

    `git remote set-url origin git@github.com:choras-org/backend.git`
3. If CHORAS runs on your machine, congratulations! You are now ready to add your own software to the platform.  

## Glossary
- **Terminal**: whenever mentioned, this can be replaced by the command you are used to (iTerm, Anaconda Prompt, PowerShell, etc.).

# Adding your own room acoustics simulation method

To see where you have to add your new simulation method, find the keyword *MyNewMethod* in the project. 

!! Please do not remove the "MyNewMethod" content! This will make eventual merging of your changes into CHORAS easier and help future developers to (just like you) find where they have to add code :)

## Backend
Add your repository as a submodule to the CHORAS repository (currently in the root of the `backend` submodule). Make sure that the repository is public so that others will be able to clone/use it too.

Add your method to the requirements.txt list using `-e` ("editable"), meaning changes to the code will immediately reflect without reinstalling. Then run

```
pip install -r requirements.txt
```
to install.

Then add your method to the following files:

#### app/models/data/simulation_settings.json
- Add a new entry to the simulation_settings.json list.
In a terminal window, navigate to the `backend` using `cd <your/path/to/CHORAS>/backend/`
- Activate the conda environment: `conda activate choras`
- Run `flask reset-db` to add the new setting to the database (TODO: do this dynamically to not lose any data).

#### example_settings/<new_file>.json
Add a new .json to the example_settings folder and define your settings based on the examples.json file.

#### app/types/Task.py
- Add your method to the TaskType(Enum)

#### simulation-backend/simulation_backend/<new_file>.py
- Add new `.py` file to the simulation-backend/simulation_backend/ folder. 

    This file will contain the interface between CHORAS and your simulation method. 
It will contain the function that will be called from app/services/simulation_service.py. The function *must* take as its argument the .json path with the user data. This will also be used to communicate information (% done, results, etc.) back to the user.

#### simulation-backend/simulation_backend/\_\_init\_\_.py
- Import everything from the interface file in the simulation-backend package \_\_init\_\_.py file: `from <your_method> import *`


#### app/services/simulation_service.py

- In the `run_solver` function import the method from the interface:     

    ```
    from simulation_backend.<your_new_interface> import <your_new_interface_method>
    ```
- Add a case to `match taskType` from which this method will be called.

    ```
    case TaskType.<your_new_tasktype>:
        <your_new_interface_method>(json_file_path=json_path)
    ```

## Frontend

Add entries to the following files:

#### src/types/index.ts
- enum ResultType

#### src/components/UI/Results/components/ResultComparison/constants.ts
- const resultTypes: Array<ResulTypeOption>

#### src/constants/index.ts
- export `const <method> string` (right above `export const RESULT_PARAMETERS`)

#### src/enums/index.ts
- enum MethodEnum

#### src/components/UI/SolverSettings/SolverSettings.tsx

- `import ... from '@/constants'`
- '<Tooltip ...>

#### src/types/index.ts
- type TaskType


## Miscellaneous

### Docker
Add the following to CHORAS/Dockerfile
```
COPY MyNewMethod/ /app/MyNewMethod
RUN pip install MyNewMethod/.
```



## Debugging (Visual Studio Code)
This section will explain how to debug (put breakpoints) in the code using Visual Studio Code (VS Code), which is a widely used lightweight IDE. 

Open VS Code and make sure you have the Python Debugger extension installed:
- In the *Extensions* tab of VS Code, find the the Python Debugger extension and install it.

In a terminal window:
- Navigate to the `backend` using `cd <your/path/to/CHORAS>/backend/`
- Run `code .` to open the current folder in VScode
- Click on the *Run and debug* tab and select *Python Debugger: Flask* from the dropdown next to the green arrow. (the settings for this are defined in <your/path/to/CHORAS>/backend/.vscode/launch.json)
- Click on the green arrow to run the Flask application in debug mode. 
- You'll now be able to put breakpoints in the application.

Note that every time you change something in the code, you'll need to click on the *Restart* icon (green arrow going around itself) in the VS Code debug panel. Possibly terminate the Celery and flask run processes using ctrl+c and run them again. 

### How to debug functions ran by Celery
The function running the simulation methods (the `run_solver` function in backend/app/services/simulation_service.py) is offloaded from the main backend and ran by Celery.

In order to debug anything that happens in run_solver, at the top of the simulation_service.py file, you'll need to set `debug_celery = True`. Note that anything asynchronous (such as the percentage update) doesn't show in the front-end while the simulation is running if this flag is indeed set to `True`.


