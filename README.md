
# Setting up jupyter notebooks on the cluster

## Step 1: Set up virtual environment
Activate python (you probably want to use whichever version is most recent):

```module load python/anaconda-2023.09```

Create an environment and make sure it is empty by using the ```no-default-packages``` flag. I have had issues with the environment not using the version of Jupyter I want if I omit this.

```conda create --no-default-packages -p /path/to/env/new_venv/```

Activate your environment and install some packages. 

```
source activate /path/to/env/new_venv/
conda install pytorch torchvision torchaudio pytorch-cuda=11.8 -c pytorch -c nvidia
conda install -c conda-forge jupyterlab numpy matplotlib
```

Choosing a pytorch with cuda is necessary if you will want to use GPU later on.

## Step 2: Modify the ```launch_jlab.sh``` script:

In this file, you may want to modify the partitions used. Here I just have a flag to open the notebook on the gpu partition instead of caslake. Typically I also have an option to use other partitions by adding this:
```
elif [ $gpu == 2 ]
then 
  PART="OTHER_PARTITION"
  GRES="gpu:1"
  QOS=""
```

You will also probably want to adjust the `PYTHON_MODULE` argument just below this.

Finally, make sure `CONDA_ENV` is set to the path you used above, in this example it would be `/path/to/env/new_venv/`.


## Step 3: Run and connect to your notebook

Launch the notebook by running `./launch_jlab.sh`. It will launch a job on the cluster with your notebook.

To access it, open a terminal on your local machine and make an ssh tunnel to the job as prompted by the output of the `launch_jlab` script.

An example is the following:
```ssh -N -f -L 8111:10.50.259.86:8111 CNET_ID@midway3.rcc.uchicago.edu```

where you should fill in your CNET ID. Note that if you want to change the port that is used (here it's 8111), you can adjust this in the `launch_jlab` script by modifying the `PORT_NUM` variable.

After this, open a browser and enter `localhost:8111` in your address bar. The first time you do this, you will be prompted to enter a token. 
This is just your CNet ID (note that this can be changed in the launch script by changing `export JUPYTER_TOKEN="$USER"`).

After this you should be good to go!


