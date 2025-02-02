# CHORAS
This is the public repository for the The Community Hub for Open-source Room Acoustics Software. Follow the steps below to run CHORAS!

## Initialising the repository
1. Clone this repository to a location of your choice.
2. In the command window / terminal, navigate (cd) to the repository.
3. Run the following line of code to check out the correct versions of the (nested) submodules:
```shell
git submodule update --init --recursive
```
4. The repository should now have the latest (correct) state.

## Backend installation
1. In the command window, run ```cd ra_ui_backend``` to navigate to the backend folder.
2. Create a new environment and install all the requirements by running the following (this will take a minute)
```shell
conda create -n RA_Backend python=3.10

conda activate RA_Backend

pip install -r requirements.txt simulation-backend/. Diffusion/. edg-acoustics/.
```

4. **Run application**
   
Once you are done with the above steps, you are ready to run the application.

```shell
# Create database
flask create-db

# Run a development server
flask run
```

You have succeeded in running the backend if you see something like this in the command window:
```
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on http://127.0.0.1:5001
Press CTRL+C to quit
 * Restarting with watchdog (fsevents)
 * Debugger is active!
 * Debugger PIN: 903-417-292
```
### Troubleshooting Mac
It might happen that when you try to run flask you get an error saying something about rhino3dm. This means that your system architecture is not compatible with the default installation of the rhino3dm package. If this happens, run `uname -a` to find your system architecture. 

```
pip uninstall rhino3dm

arch <arch> pip install rhino3dm
```
where `<arch>` should be replaced by the architecture of your system (such as `-x86_64` or `-arm64`).

## Frontend installation
1. Install node on your machine (https://nodejs.org/en).
2. Once installed, navigate to `ra_ui_frontend` in the command window.
3. (Windows only) Run `npm install -g node-gyp`
4. (Windows only) Download and install GTK following step 2 via https://github.com/Automattic/node-canvas/wiki/Installation:-Windows. Be sure to unzip it in `C:/GTK`.
5. In the command window, run `npm install`.
5. Run the front-end app using `npm run dev`
6. Go to http://localhost:5173/ and the user interface should be visible.

## Running your first simulation
...work in progress...
