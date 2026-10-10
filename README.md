# Bool-T2DM
Simulation of a Boolean Network for Type 2 Diabetes

Bool-T2DM is a Python package for the simulation and analysis of dynamic Boolean network models.

This package is developed as a fine-tuned variant of the open-source [SimpleBool](https://github.com/lujunyan118/SimpleBool) toolkit. The original SimpleBool framework was inspired by the pioneering work of István Albert and Réka Albert on biological Boolean networks. Similar to their foundational Python package [Booleannet](http://code.google.com/p/booleannet/), Bool-T2DM updates each node state using a pre-computed truth table and employs a streamlined Boolean model representation. It requires no prior coding experience for standard use, and delivers faster runtime performance compared with Booleannet. That said, for advanced users seeking greater flexibility and support for sophisticated updating algorithms (e.g., the piecewise differential method), Booleannet remains the recommended option.

## Requirements
Bool-T2DM works on Python 2.7 (Python 3.0 or higher is not supported). 

A sample model input file of a Boolean network model describing the development of Type 2 Diabetes: [T2DM.txt]

### Running simulations and plotting using BoolSimu.py
For using BoolSimu.py to perform dynamic simulations on a Boolean network model and plot the simulation results, a parameter input file named 'simu.in' is needed to specify the simulation and plotting information.

The "simu.in" file looks like:
```
rules = T2DM.txt
turn_on = DC
turn_off =
ini_on = GPR40,GLP-1
ini_off = Proliferation,Apoptosis
mode = Sync
rounds = 500
steps = 30
plot_nodes = Apoptosis,GS,GLUT4,FOXO1,GSK3belta
missing = random
```

Run the script by typing "python BoolSimu.py simu.in".

**rules**: 'rules' specifies the model input file ('T2DM.txt').

**turn_on**, **turn_off**: 'turn_on' and 'turn_off' each accepts a list of node names separated by ','. These two parameters specify the nodes that need to be **kept** in ON state or OFF state during simulation.

**ini_on**, **ini_off**: 'ini_on' and 'ini_off' each accepts a list of node names separated by ','. These two parameters specify the nodes whose states should be set in ON or OFF states **at the first step** of simulation.

**mode**: this parameter specifies the updating method. Three methods are implemented: 'Sync' (Synchronous updating method), 'GA' (General Asynchronous updating method) and 'ROA' (Random Order Asynchronous updating method). For the detailed explanation of these three methods, please consult the papers [Attractor analysis of asynchronous Boolean models of signal transduction networks](http://www.sciencedirect.com/science/article/pii/S0022519310003796) by Assieh Saadatpoura, István Albertb and Réka Albert.

**rounds**: 'rounds' specifies the rounds of simulation started from randomly selected initial states.

**steps**: 'steps' specified the steps of iteration during each round of simulation.

**plot_nodes**: this parameter specifies the nodes whose simulation results needed to be plotted when the simulation is done.

**missing**: this parameter specified the states of those nodes whose initial states are not determined from 'turn_on','turn_off','ini_on' and 'ini_off'. Values can be 'random', 'True', or 'False'.

* The parameter file, model input file should be in the same folder. 


### Performing node perturbation studies using BoolMutation.py

A parameter input file, named "mutation.in", is needed to perform the node perturbations using BoolMutation.py

The "mutation.in" file looks like:
```
rules = T2DM.txt
turn_on = GIP
turn_off =
ini_on = OS,ER
ini_off = TNFalfa, JNK
rounds = 500
steps = 100
mode=ROA
mutation_list=list_ON.txt,list_OFF.txt
keep_state=False,True
mutation_mode=single
observe_list=Apoptosis,GS,GLUT4,FOXO1,GSK3belta
```

Run the script by typing "python BoolMutation.py mutation.in".

This first eight parameters are the same as 'simu.in' used by BoolSimu.py

**mutation_list**: 'mutation_list' specifies the files that contain the nodes that need to be perturbed. Sample input list of nodes: [list_ON.txt]

**keep_state**: 'keep_state' specifies the perturbed state of the nodes in each nodes list provided by 'mutation_list' parameter, accordingly.

**mutation_mode**: this parameter denotes whether single or double perturbation study should be performed. 

**observe_list**: this parameter specifies the nodes whose final states should be written out in the output file.

* BoolMutation.py writes out a .csv file that contain the perturbed nodes and their states, as well as the states of the nodes specified in the 'observe_list'.

### Identifying attractors and basins of attraction using BoolAttractor.py

Similarly, a parameter input file, "steady.in", is needed by BoolAttractor.py

The "steady.in" file looks like:

```
rules = T2DM.txt
turn_on =
turn_off =
ini_on = 
ini_off = 
mode=Sync
steps=100000
rounds=100
initial_limit=0
```
This first eight parameters are the same as 'simu.in' used by BoolSimu.py

**initial_limit**: this parameter specifies the maximum number of initial states generated when constructing the state transition graph. "0" means no limitation and BoolAttractor.py this will use all the possible initial states to construct the state transition graph. For a Boolean network with n nodes, the number of states is 2^n. Therefore, for large Boolean networks, using all the possible initial states may be extremely slow or run into memory problems.  

* When finished, a folder named 'Data' containing the all result files is generated in the working directory.

* These results include the whole state transition graph ('**TransGraph.txt**'), the information of point attractors and cyclic attractors ('**Point_attractors.csv**' and '**Cyclic_attractors.csv**'), summarization of the basins of attractors and basin intersections ('**Summary_basin.txt**'), and the nodes that stabilized on a certain state in all the attractors ('**Fixed_nodes**').
