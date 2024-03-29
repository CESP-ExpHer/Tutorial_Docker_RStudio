# Practical Tutorial for Creating RStudio with Docker
Created by: Yazdan Asgari<br>
Creation date: 29 Mar 2024<br>
Update: Mar 2024<br>
https://cesp.inserm.fr/en/equipe/exposome-and-heredity
<br>
<br>
**IMPORTANT NOTE:** This tutorial has been tested on **CentOS 7**.

In this tutorial, we want to create a Docker image including the following features
* Having RStudio with three installed packages (**"vroom"**, **"dplyr"**, and **"ggplot2"**)
* Creating a **"file"** folder and putting three files (*Thyroid_cancer_snplist.RData* and *C1_code_find_common_snps_one_pair.R*) in the **"file"** folder
* After building, map a folder on your host machine to the home directory within the Docker container
  
**Steps:**
* Create a folder called **"test_docker_2"** in your Host Machine.
* In the **"test_docker_2"** folder, Create a folder called **"files"** and put two files (*Thyroid_cancer_snplist.RData* and *C1_code_find_common_snps_one_pair.R*) in it.
* In the **"test_docker_2"** folder, Create a file called **"dockerfile"** and write the following lines in it:
```
# Using the RStudio image
FROM rocker/rstudio:3.6.3

# Creation of "files" folder in the Image
RUN mkdir -p /home/files

# Copying two files to the "files" folder in the Image
COPY files/Thyroid_cancer_snplist.RData /home/files/
COPY files/C1_code_find_common_snps_one_pair.R /home/files/

# Installing three packages in the RStudio
RUN R -e 'install.packages(c("vroom", "dplyr", "ggplot2"), repos="https://cloud.r-project.org")'
```
* Now it is time to build the Docker image. In the terminal, go to the **PATH** for **"test_docker_2"** folder using **cd** command
~~~
cd PATH_IN_YOUR_HOST_MACHINE/test_docker_2
~~~

* Check that you could be able to see **dockerfile** in this **PATH**:
~~~
ls
~~~
* Run the following command for building the Docker image with an arbitrary name (here we used **"test_rstudio"** for the name):
~~~
sudo docker build -t test_rstudio .
~~~
* If all goes well without any error, you will be able to see the created Docker image (**"test_rstudio"**) with this command:
~~~
sudo docker images
~~~
* Now you could run the Docker image. We use the following command which contains some options:
  * Mapping any arbitrary port (here we used **8788**) to the default port of the RStudio (**8787**) using **-p** option
  * Definition of a password for RStudio (here **123**) using **-e** option
  * Linking an existing folder (**"docker_test_share_folder"**) in your host machine (here **"PATH_IN_YOUR_HOST_MACHINE/docker_test_share_folder"**) to the RStudio home folder in Docker container (**"/home/rstudio"**) using **-v** option
~~~
-v PATH_IN_YOUR_HOST_MACHINE/docker_test_share_folder:/home/rstudio
   <---------------------host----------------------->:<-container->
~~~
* So, run the following command to apply all the above options:
~~~
sudo docker run -d -p 8788:8787 -e PASSWORD=123 -v PATH_IN_YOUR_HOST_MACHINE/docker_test_share_folder:/home/rstudio test_rstudio
~~~
* If it runs without any error, you see a string of characters and it goes to the next line.
* Now, open a web browser and type the following in the address bar (You could also use your local IP address instead of **"localhost"**):
```
http://localhost:8788
```
* For username, type **"rstudio"** and for the password, type **"123"**. Now you can use the RStudio with pre-installed three packages.
