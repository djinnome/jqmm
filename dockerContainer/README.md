# Installing all jQMM dependencies using Docker (https://www.docker.com/)
Docker provides a way to increase reproducibility in our results. Docker containers wrap a piece of sofware in a complete file system containing everything needed to run: code, systems libraries, systems tools and anything else that can be installed in a server. This guarantees that it will always run correctly adn in the same way, regardless fo the system environment it is running in. The jQMM docker container can be run on any server and any cloud computing serviced such as Amazon Web Services (AWS), Google Cloud Platform or Microsoft Azure. This container does NOT include GAMS, CPLEX or CONOPT licenses, which must be provided by the user.

Here are the instructions for a jQMM installation through docker:

## Compile and upload docker container
docker build -t mhgarci1/jqmm:latest .
docker login
docker push mhgarci1/jqmm

See https://github.com/JBEI/jqmm for license, source code, and instructions.

The following instructions explain how to launch a jQMM docker container. jQMM uses the General Algebraic Modeling System (GAMS), and requires a valid GAMS license for solving all but the simplest models. Place your license file (which must be named "gamslice.txt") in the current folder before following these instructions. You can purchase a license here: https://www.gams.com/

# 1) Launch container:

docker run --name jqmm -p 8888:8888 -d mhgarci1/jqmm

# 2) Install GAMS:

docker exec -it jqmm ./install_gams.sh

# 3) Copy in the gamslice.txt license from the current folder. ***NOTICE:*** This command will only work if you have a valid GAMS license, and run the command from within a folder already containing that license, which must be named "gamslice.txt". If you do not have a valid GAMS license, this step can be skipped and GAMS will operate in trial mode, where only small models can be solved.

docker cp gamslice.txt jqmm:/gams/gamslice.txt

# 4) Run the GAMS script which finalizes the GAMS install. Ignore the instructions and questions on the screen, as our workflow performs those steps automatically.

docker exec -it jqmm bash -c 'cd /gams; yes 1 | ./gamsinst'

# 5) Stop container, restart interactively, and  manually open a browser to the url it suggests:

docker stop jqmm
docker start -i jqmm

# Optional: to completely uninstall jQMM run the following three commands.

docker stop jqmm
docker rm jqmm
docker rmi mhgarci1/jqmm