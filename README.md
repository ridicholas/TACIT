# TACIT

This repository contains code for the Topic-Aware, Community-Impacted Twitter simulator, as introduced in the paper: [Does AI-Assisted Fact-Checking Disproportionately Benefit Majority Groups Online](https://arxiv.org/abs/2302.03782) [In Proceedings @ Facct '23].

## TACIT Simulation Description

TACIT is a novel agent-based methodology for simulating the spread and consumption of multi-topic information across networks. Our simulation approach is able to generate behavior on networks with varying structures and 'states of the world' while retaining realistic information propagation and consumption characteristics. Given its realistic data generating process, TACIT serves as a transparent test bed for check-worthiness algorithms. A detailed description of TACIT can be found in our research paper. 


## Class/Function Descriptions

The code for running a TACIT simulation is primarily contained in src/TopicSim.py and src/checkworthy.py. The run.sh and src/main.py files can be used to run the simulations contained in the paper. 

### TopicSim.py

The `TopicSim` class contains all information relevant to the state of the world throughout the simulation such as the network itself (nodes and edges), the belief and impactedness values of all nodes at each timestep, and the information that has been created, consumed, and propagated throughout the network. The `run` function serves as the simulation engine that progresses the simulation through the specified number of timesteps. Because `TopicSim` stores the state of the network, the run function can be used to begin a new simulation with an input network or it can be used to continue a simulation that had run previously. For example, we can run a `TopicSim` simulation with no check-worthiness mitigation implemented, and once it completes, we can restart it with a mitigation strategy selected. 

### checkworthy.py

This file contains the `CheckWorthy` class, which tabulates the dataset used for the checkworthiness models according to how the `TopicSim` simulation progresses. Each `TopicSim` object contains a `CheckWorthy` object. The `CheckWorthy` object contains the checkworthy dataset, stored in a `pandas` dataframe, and it is updated at each iteration of the `TopicSim` based on the information that was consumed and propagated. `CheckWorthy` also contains all necessary functions to update the checkworthy dataset. 

### main.py

`main.py` can be used to run a two-stage TACIT simulation as demonstrated in the research paper. `main.py` should be run with arg specifications that determine what kind of checkworthiness mitigation will be implemented during the run. When running `main.py`, specify the mitigation_method (-m), label_method (-l), and sample_method (-s) that will be used along with the period (-p). The mitigation_method corresponds to diversity variable 3, the label_method corresponds to diversity variable 2, and the sample_method corresponds to diversity variable 1. All other runtime parameters are read in from the `config.yaml` file. 

### run.sh
`run.sh` contains an example of how `main.py` can be called with different arg settings to generate the simulation results found in our paper. The `run.sh` file first runs a pre-period simulation with no mitigation implemented. Next, it runs all combinations of mitigation methods, label methods, and sample methods in parallel continuing from the pre-period simulation. Files are output into the output/ folder. 


## Data
We ran TACIT on a subset of a publicly available twitter network dataset that accounts for roughly 75% of the 450,000 Twitter users first analyzed in De Domenico et al. (2013). The dataset can be found at: https://snap.stanford.edu/data/higgs-twitter.html. We first ran the Lovaine community detection algorithm on this dataset and assigned each node a 'Community' attribute. The code in this repository can be run on any network (made of nodes and edges) that contains a community structure (each node should be assigned a 'Community' in its 'Community' attribute). 

## Running TACIT
main.py provides basic example code for running a single TACIT simulation, while `run.sh` shows how main.py can be called in sequence and in parallel to generate simulation results for different checkworthiness mitigation strategies on the same network. Currently filepaths are configured in the `config.yaml` file and correspond to the higgs dataset we used in our experiments, so changes might need to be made to adapt to a new dataset. Current `config.yaml` settings are set up for a smaller version of the experiment shown in the paper to save on processing time (fewer nodes, fewer iterations, etc.). 

### Loading Data
In the `config.yaml` file, in addition to setting simulation runtime settings, you can choose how you'd like to work with the available dataset. If `load_data == True & random_communities == False`, `main.py` will directly load the data from `ready_network_path` and proceed with the simulation using this data. If `load_data == False`, `main.py` will load the data from `raw_network_path` and proceed to subset the data. If `random_communities == True`, `random_community_sample(community_graph, min_network_size=100000, max_network_size=200000)` will run, requiring a community_network file in which each comminity is a node with a 'SIZE' attribute specifying how many nodes are in that community. The random subsetting will then randomly select communities while fitting with the minimum and maximum network size parameters. If `random_communities == False`, the communities specified in `communities` will be selected. The network will further be randomly subset to only include `perc_nodes_to_subset`% nodes. 


### Output
The main.py file generates a `TopicSim` object and progresses it through the simulation. Once the simulation completes, the `TopicSim` object is stored in a .pickle file and saved in the output/ folder. 

# Requirements
All requirements found in `requirements.txt`. Code was written using Python 3.10.6. 




