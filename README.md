# CHORAS
This is the public repository for the The Community Hub for Open-source Room Acoustics Software. Follow the steps below to run CHORAS!

## Initialising this repository
1. Clone this repository to a location of your choice.
2. In the command window / terminal, navigate (`cd`) to the repository.
3. Run the following line of code to check out the correct versions of the (nested) submodules:
```
git submodule update --init --recursive
```
4. The repository should now have the latest (correct) state.
5. Every time you make changes to submodules, remember to run 
```
git submodule update --recursive
```
to include the correct commits of all the submodules.

## Backend installation
1. In the command window, run ```cd ra_ui_backend``` to navigate to the backend folder.
2. Create a new environment and install all the requirements by running the following (this will take a minute)
```shell
conda create -n RA_Backend python=3.10

conda activate RA_Backend

pip install -r requirements.txt simulation-backend/. Diffusion/. edg-acoustics/.
```
3. Create the database by running `flask create-db` (you only have to do this once).

4. Run the application by running `flask run`.

You have succeeded in running the backend if you see something like this in the command window:
```
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on http://127.0.0.1:5001
Press CTRL+C to quit
 * Restarting with watchdog (fsevents)
 * Debugger is active!
 * Debugger PIN: 903-417-292
```
5. Note that in order to run the application correctly, this command window should remain open with the `flask run` process running.

### Troubleshooting Mac
It might happen that when you try to run flask you get an error saying something about rhino3dm. This means that your system architecture is not compatible with the default installation of the rhino3dm package. If this happens, run `uname -a` to find your system architecture. 

```
pip uninstall rhino3dm

arch <arch> pip install rhino3dm
```
where `<arch>` should be replaced by the architecture of your system (such as `-x86_64` or `-arm64`).

## Frontend installation (open a new command window)
1. Install node on your machine (https://nodejs.org/en).
2. Once installed, navigate to `ra_ui_frontend` in the command window.
3. (Windows only) Run `npm install -g node-gyp`
4. (Windows only) Download and install GTK following step 2 via https://github.com/Automattic/node-canvas/wiki/Installation:-Windows. Be sure to unzip it in `C:/GTK`.
5. In the command window navigated to the `ra_ui_frontend` directory, run `npm install`.
5. Run the front-end app using `npm run dev`.
6. Go to http://localhost:5173/ in your favourite browser and the user interface should be visible.
7. To interact with the user interface, make sure that the `flask run` process is (still) running in a separate command window.

## Running Celery (open a new command window)
Celery is a package that allows for distributed task queueing. In the case of CHORAS, it allows to offload the simulation to a separate "worker" so that other processes (such as queueing other tasks) will not be blocked.

To run Celery:
1. Open a new command window and navigate to the `ra_ui_backend` folder.
2. Activate the previously created environment by running `conda activate RA_Backend`.
3. Run Celery by running:
```
celery -A app.celery worker --loglevel=info -P eventlet
```
4. You should see something like:

```
 -------------- celery@TUE031950 v5.4.0 (opalescent)
--- ***** -----
-- ******* ---- Windows-10-10.0.19045-SP0 2025-02-02 14:47:11
- *** --- * ---
- ** ---------- [config]
- ** ---------- .> app:         ui_backend:0x2bc5d65f640
- ** ---------- .> transport:   sqla+sqlite:///C:\Users\20225896\repositories\CHORAS\ra_ui_backend\celerydb.sqlite
- ** ---------- .> results:     sqlite:///C:\Users\20225896\repositories\CHORAS\ra_ui_backend\celerydb.sqlite
- *** --- * --- .> concurrency: 12 (eventlet)
-- ******* ---- .> task events: OFF (enable -E to monitor tasks in this worker)
--- ***** -----
 -------------- [queues]
                .> celery           exchange=celery(direct) key=celery


[tasks]
  . app.services.simulation_service.run_solver

[2025-02-02 14:47:11,628: INFO/MainProcess] Connected to sqla+sqlite:///C:\Users\20225896\repositories\CHORAS\ra_ui_backend\celerydb.sqlite
[2025-02-02 14:47:11,645: INFO/MainProcess] celery@TUE031950 ready.
```

## Running your first simulation
Before running your first simulation, make sure to have three command windows running the following processes:

| Process | Root folder | Conda environment | Command |
|---|---|---|---|
| Flask backend | ra_ui_backend | `RA_Backend` | `flask run` |
| Celery | ra_ui_backend | `RA_Backend` | `celery -A app.celery worker --loglevel=info -P eventlet`
 | React frontend | ra_ui_frontend | doesn't matter | `npm run dev` | 

Once these three processes are running in their respective windows follow these steps:
1. Click on "Import geometry (model)".
2. Import the example .obj file which can be found here: `CHORAS/ra_ui_backend/example_models/MeasurementRoom.obj`.
3. Enter a name for the model, assign it to a new group and a new project (choose names as you like).
4. Click on the "Open model" button.
5. Click on "Create a simulation" and give it a name.
6. Upload the example .geo file which can be found here: `CHORAS/ra_ui_backend/example_models/MeasurementRoom.geo`, by clicking on the "Drop the file here to upload" button.
    - Currently, the .geo file upload process stops the `flask run` process, so restart this by killing it using Ctrl+C in its command window, and rerunning `flask run` thereafter. 
    - Make sure to refresh the browser (http://localhost:5173) after doing this.
7. Add a source and receiver by clicking on the respective "+" buttons.
8. Add materials to all surfaces by clicking on the "Materials" tab, selecting the layers, and finally selecting a material by double-clicking it in the list that just popped up.
9. Select the "Settings" tab, click "Advanced", scroll down and select "Diffusion Equation method (DE)" from the list of "Available Methods".
10. Select "Run the simulation" and "Start Simulation" thereafter. (After a few seconds, the progress bar should start updating)
11. Click on the "Results" button (bottom left) to show the results of the simulation.