**Learning and Practicing Docker**

1. **Learning Docker**
   1. **What is Docker?**

      Docker is a virtualization software. It is a container runtime technology that allows you to build, test, and deploy applications faster than traditional methods.

   1. **What’s the benefit of using Docker?**

      For our project, Docker simplifies DevOps processes by integrating seamlessly with CI/CD pipelines, enhancing deployment efficiency and reliability. It is lightweight and efficient, using fewer resources compared to virtual machines. 

1. **Step-by-Step Process**
1) **Setting up Docker and environment**

   We can install the Docker Desktop from the Docker official website.

   ![](images/Aspose.Words.c1a068db-197f-49e4-8b03-4f02c5c1b9d2.001.png)

   ![](Aspose.Words.c1a068db-197f-49e4-8b03-4f02c5c1b9d2.002.png)

   Then we need to use Visual Studio Code and install a Docker extension for it

   ![](Aspose.Words.c1a068db-197f-49e4-8b03-4f02c5c1b9d2.003.png)

1) **Create a new workspace named “main.py” in the VS Code new project.**

   ![](Aspose.Words.c1a068db-197f-49e4-8b03-4f02c5c1b9d2.004.png)

   In the python terminal, Use the “cd example1” to tap into my new directory

   ![](Aspose.Words.c1a068db-197f-49e4-8b03-4f02c5c1b9d2.005.png)

   Copy and paste any pre-existing Python application code into “main.py”. 

   In this case, I’m just using “Hello World” for testing.

   ![](Aspose.Words.c1a068db-197f-49e4-8b03-4f02c5c1b9d2.006.png)

1) **After creating the python app, we need the basic Docker components for it to function properly.**

First we create a “Dockerfile” in the new directory.

` `![](Aspose.Words.c1a068db-197f-49e4-8b03-4f02c5c1b9d2.007.png)

Then, we use this command to pull down a tagged python base image

![](Aspose.Words.c1a068db-197f-49e4-8b03-4f02c5c1b9d2.008.png)

Then we add our python file into Docker using “ADD”.

![](Aspose.Words.c1a068db-197f-49e4-8b03-4f02c5c1b9d2.009.png)

After that, we enter the command that will execute once the container has started

![](Aspose.Words.c1a068db-197f-49e4-8b03-4f02c5c1b9d2.010.png)


1) **Build the image within VS Code.**

   We can build the image using the command “docker build -t imagename .”

   ![](Aspose.Words.c1a068db-197f-49e4-8b03-4f02c5c1b9d2.011.png)

   After that, we can run our Python application using the docker we just built.

   ![](Aspose.Words.c1a068db-197f-49e4-8b03-4f02c5c1b9d2.012.png)

   As we can see, it outputs the “Hello World!” as my app was designed. We can also find the Container we built in run Desktop App.

![](Aspose.Words.c1a068db-197f-49e4-8b03-4f02c5c1b9d2.013.png)





