# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program

---

In this project I decide the steering angle and speed of the simulator car , using MPC (Model Predictive Control)

#### default project installation :
1. mkdir build
2. cd build
3. cmake ..
4. make
5. ./mpc

#### my own Build Instructions
I was using Windows 10 and VisualStudio17

-to build this project using **Bash for window** :

    navigate to projet
    write cmd : mkdir build
    then navigate to build
    write cmd : cmake .. -G "Unix Makefiles" && make
    write cmd : ./mpc

# MPC
####       The Model

MPC is a model that optimizes the controls of a vehicle ( steering and speed ) by reducing the cost (error comparing to desired trajectory)

Any model cannot be 100% perfect, but MPC is close to perfection because it keeps refreshing the predection of the controls 

MPC only excute the first set of controls, and then repeat the process to predect a new state.

1. Setup the duration of the trajectory T = N * dt
   while N = number of steps, and dt is constant delta-time of each step.

2. Setup the model :
   a.  find intital [ x , y , psi , v , cte , epsi ], this is the initial state
   b.  predect the next x , y , psi , v , cte , and epsi

           // Recall the equations for the model:
           // x_[t+1] = x[t] + v[t] * cos(psi[t]) * dt
           // y_[t+1] = y[t] + v[t] * sin(psi[t]) * dt
           // psi_[t+1] = psi[t] + v[t] / Lf * delta[t] * dt
           // v_[t+1] = v[t] + a[t] * dt
           // cte[t+1] = f(x[t]) - y[t] + v[t] * sin(epsi[t]) * dt
           // epsi[t+1] = psi[t] - psides[t] + v[t] * delta[t] / Lf * dt
    c. find the constrains :
            tune "delta" & "a" , which are related to stearing
3. Feed the resulting values to the cost function, which will result #N sets of controls
4. Only apply the first set of contols , and repeat the process again

#### Timestep Length and Elapsed Duration (N & dt)

we have to tune N & dt taking in concedration that more N means more processing.
also less dt means more proccessing, and the aplication will run slow.

so we need the best value of N and dt  that will cause cross track error to decrease to 0

I tried diffrent values of N, ex: N = 20 cause the car to take wrong decisions in straight lines, and green debug line was crasy !
Finaly I set these values to N = 8, and dt = 0.1


#### Polynomial Fitting and MPC Preprocessing

Third degree polynomial is required to set the cte and epsi values.

#### Model Predictive Control with Latency

Latency in simulator = 100ms , that was set to simulate natural conditions
of course this will effect the car dicisions, because there's 100ms delay between predection and sending it to the server.

to handel this I multiply initial x , y , psi, and v by the latency value
           
   
    px = px + v*cos(psi)*latency;
	py = py + v*sin(psi)*latency;
	//psi = psi + v*delta / Lf*latency;//Drives the car crazy !
	v = v + acceleration*latency;

I hade troubles making psi works, so I ommitted it.

#### results :

car can take many loops around the track,

*gif animation :*
   
   ![curve 03](https://github.com/anasmatic/CarND-Term2-project5-MPC/blob/master/res/_03.gif)

it can handle sharp turns and recover after them


*gif animation :*
   
   ![curve 02](https://github.com/anasmatic/CarND-Term2-project5-MPC/blob/master/res/_02.gif)


only one problem , that is starts wiggeling at the start of the track, but stop wiggle right after it approches the first turn


*gif animation :*
   
   ![wiggle](https://github.com/anasmatic/CarND-Term2-project5-MPC/blob/master/res/_01.gif)

*video of one lap :*
   ![Full Lap](https://github.com/anasmatic/CarND-Term2-project5-MPC/blob/master/res/vid.mp4)
