.. basic-app

Basic App: Hello World
======================

GeneFlow workflows are composed of applications, or apps, which are modular pieces of functionality. Apps are ideally designed so that they can be combined to produce useful data analysis workflows. This tutorial covers the creation of a basic GeneFlow app that prints "Hello World!" to a text file. It is meant to be an introduction to the basic features of GeneFlow apps and does not cover all features. Advanced GeneFlow app features will be covered in later tutorials.

Configure the Environment
-------------------------

Load or Install GeneFlow
~~~~~~~~~~~~~~~~~~~~~~~~

Configure the Linux environment by loading or installing GeneFlow. To check if GeneFlow is available, use the following command:

.. code-block:: text

    geneflow --help

If it's available, you should see GeneFlow's usage instructions:

.. code-block:: text

    usage: geneflow [-h] [--log_level LOG_LEVEL] [--log_file LOG_FILE]
                    {add-apps,add-workflows,help,init-db,install-workflow,make-app,migrate-db,run,run-pending}
                    ...

    GeneFlow CLI

    positional arguments:
      {add-apps,add-workflows,help,init-db,install-workflow,make-app,migrate-db,run,run-pending}
                            Functions
        add-apps            add apps to database
        add-workflows       add workflows to database
        help                GeneFlow workflow help
        init-db             initialize database
        install-workflow    install workflow
        make-app            make app from templates
        migrate-db          migrate database
        run                 run a GeneFlow workflow
        run-pending         run pending workflow jobs

    optional arguments:
      -h, --help            show this help message and exit
      --log_level LOG_LEVEL
                            logging level
      --log_file LOG_FILE   log file

However, if it's not installed, you'll get an error message like this:

.. code-block:: text

    -bash: geneflow: command not found

If your system is configured with modules, try loading the GeneFlow module:

.. code-block:: text

    module load geneflow/latest
    geneflow --help

If you need to install GeneFlow, the recommended method for installation is in a Python virtual environment, as described here: :ref:`Install GeneFlow using a Python Virtual Environment <install-geneflow-venv>`.

After installation in a Python virtual environment, load GeneFlow by sourcing the virtual environment:

.. code-block:: text

    cd ~/geneflow_work
    source gfpy/bin/activate

Clone the GeneFlow App Template
-------------------------------

Create the "geneflow_work" directory in your home directory if it doesn't already exist. This will be the location for all tutorial-related workflows, apps, and data:

.. code-block:: text

    mkdir -p ~/geneflow_work
    cd ~/geneflow_work

GeneFlow's public Apps and Workflows repository is located here: https://gitlab.com/geneflow/. In addition to public apps and workflows, this repository contains app and workflow templates. When creating new GeneFlow apps or workflows, we recommend to start with the app or workflow template rather then from scratch. Clone the app template using git:

.. code-block:: text

    git clone https://gitlab.com/geneflow/apps/app-template-gf2.git hello-world-gf2

This command downloads the app template into the "hello-world-gf2" directory. "hello-world-gf2" also happens to be the name of the app you're creating in this tutorial.

The GeneFlow app template contains a simple, but fully functional application. View the contents of the app template using the "tree" command:

.. code-block:: text

    cd hello-world-gf2
    tree .

You should see the app template directory structure similar to the following (it may have some extra files):

.. code-block:: text

    .
    ├── assets
    │   └── README.rst
    ├── app.yaml
    ├── README.rst
    └── test
        ├── data
        └── README.rst

    5 directories, 7 files

You'll need to update the "app.yaml" file to create the "Hello World" app. The "app.yaml" file is the main app definition file, which defines the inputs, parameters, and execution commands of the app.

It's good practice to also update the main "README.rst" file to document the app. 

Define the App
--------------

Configure the app by editing the "app.yaml" file. This file currently contains the definition of a fully functional app, so you'll be simplifying some of the sections to create the "hello-world" app. Open the "app.yaml" file using your favorite text editor (vi and nano examples shown):

.. code-block:: text

    vi ./app.yaml

or:

.. code-block:: text

    nano ./app.yaml

The "app.yaml" file contains three main sections: Metadata, Inputs and Parameters, and Execution Methods. Edit each of these sections to create the "hello-world" app.

Metadata
~~~~~~~~

The app metadata section contains the following basic information:

name:
  Name of the GeneFlow app. The app name should include a "gf2" suffix. For example, if the app is meant to wrap the "mem" function in BWA, the app name should be "bwa-mem-gf2". For this example, use "hello-world-gf2". 

description:
  A title or short description of the app. For this example, use "Simple hello world GeneFlow app".

git:
  The full URL of the app's source repository. This information is not available yet, so leave it blank.

version:
  A string value that represents the app's version. For this example, use "0.1". We recommend to start with "0.1" for new apps and increment the number when changes are made to the app. 

In the "app.yaml" file, modify the "Metadata" section so that it looks like the following:

.. code-block:: yaml

    # name: standard GeneFlow app name
    name: hello-world-gf2
    # description: short description for the app
    description: Simple hello world GeneFlow app
    # git: link to the app's git repo
    git:
    # version: must be incremented every time this file, or any file in the app
    # project is modified
    version: '0.1'

Inputs and Parameters
~~~~~~~~~~~~~~~~~~~~~

Each app input and parameter item is defined in a subsection with several properties. At least one input and one parameter is requred for each app. The "output" parameter is required, and must be manually included in the config file.

The example "Hello World" app doesn't need any inputs. However, because at least one input is required, define a "dummy", or un-used, input called "file". Modify the "Inputs and Parameters" section of the "config.yaml" file so that it looks like the following:

.. code-block:: yaml

    inputs:
      file:
        label: Dummy Input File
        description: Dummy input file
        type: File
        required: false

    parameters:
      output:
        label: Output Text File
        description: Output text file
        type: File
        required: true
        test_value: output.txt

For a more detailed explanation of each input or parameter property, see :ref:`App Inputs and Parameters <apps-inputs-parameters>`.

Execution Methods
~~~~~~~~~~~~~~~~~

The "Execution Methods" section of the app configuration file defines what the app actually does when executed. Apps can be defined with multiple execution methods. The specific method executed upon app invocation is either auto-detected or specified on the command line. Execution method names are customizable and the choice of a name should depend on the execution system. For example, if the app dependencies are installed globally in the execution system, use an execution method called "environment" (indicating that dependencies are available in the environment). If the app dependencies are containerized with Singularity, use an execution method called "singularity". For a more detailed explanation of the app "Execution Methods" section, see :ref:`App Execution Methods <app-execution-methods>`.

The "Execution Methods" section of the "app.yaml" file contains three sub-sections: "pre_exec", "exec_methods", and "post_exec".

The "pre_exec" sub-section defines any commands that should be executed prior to commands in the main "exec_methods" sub-section. These usually include commands for directory or file preparation that are common for all execution methods, e.g., creating an output directory. For this tutorial, no "pre_exec" commands are required, so leave it blank:

.. code-block:: yaml

    pre_exec:

The "Hello World" app simply prints "Hello World!" to a text file using the standard Linux "echo" command. Thus, define a single execution method in the "exec_methods" sub-section called "environment", which indicates that the needed commands or tools are already available in Linux. Update the "exec_methods" sub-section so that it looks like the following:

.. code-block:: yaml

    exec_methods:
    - name: environment
      if:
      - in_path: 'echo'
      exec:
      - run: echo 'Hello World!'
        stdout: ${OUTPUT_FULL}

The "if" statement is used for auto-detecting the execution method. If multiple execution methods are specified, the first execution method with an "if" statement that evaluates to "True" will be selected for execution. In this example, the statement ``in_path: 'echo'`` within the "if" statement means that the "environment" execution method will be selected if the "echo" command is available in the environment path. The "exec" statement contains a list of commands to be executed for the "environment" execution method. The "environment" execution method contains only a single command that echos the "Hello World!" text to an output file. Here, ${OUTPUT_FULL} is the full path of the file specified by the "output" parameter.

The "post_exec" sub-section defines any commands that should be executed after commands in the main "exec_methods" sub-section. These usually include commands for cleaning up any temporary files created during app execution. For this tutorial, no clean-up commands are necessary, so leave it blank:

.. code-block:: yaml

    post_exec:

"Make" the App
--------------

Now that the app has been configured, generate the app wrapper script, the test script, and various definition files using the following commands:

First, make sure you're still in the app directory:

.. code-block:: text

    cd ~/geneflow_work/hello-world-gf2

Then run the GeneFlow "make-app" command:

.. code-block:: text

    geneflow make-app .

GeneFlow will then generate three files:

.. code-block:: text

    2019-05-31 00:21:43 INFO [app_installer.py:293:make_agave()] compiling /home/[user]/geneflow_work/hello-world-gf/agave-app-def.json.j2
    2019-05-31 00:21:43 INFO [app_installer.py:325:make_wrapper()] compiling /home/[user]/geneflow_work/hello-world-gf/assets/hello-world-gf2.sh
    2019-05-31 00:21:43 INFO [app_installer.py:357:make_test()] compiling /home/[user]/geneflow_work/hello-world-gf/test/test.sh

Test the App
------------

The GeneFlow "make-app" command generates a "test.sh" script inside the "test" folder. If your app requires test data, that data can be placed inside the "test" folder, ideally within a sub-folder called "data". In this example, no test data is required.

To test the app, run the following commands:

.. code-block:: text

    cd test
    sh ./test.sh

You should see output similar to the following:

.. code-block:: text

    CMD=/home/[user]/geneflow_work/hello-world-gf/test/../assets/hello-world-gf.sh --output="output.txt" --exec_method="auto"
    File:
    Output: output.txt
    Execution Method: auto
    Detected Execution Method: environment
    CMD=echo 'Hello World!'  >"/home/[user]/geneflow_work/hello-world-gf/test/output.txt"
    Exit code: 0
    Exit code: 0

The "output.txt" file should also have been created in the test directory with the text "Hello World!". View it with:

.. code-block:: text

    cat ./output.txt

And you should see this output:

.. code-block:: text

    Hello World!

Update the App README
---------------------

It is best practice to update the app README file to include the app name, a short description, and descriptions for each input and parameter. Edit the README.rst file in the main app directory:

.. code-block:: text

    cd ~/geneflow_work/hello-world-gf2
    vi ./README.rst

Modify the file so it looks like the following:

.. code-block:: text

    Hello World! Basic GeneFlow App
    ===============================

    Version: 0.1

    This is a basic GeneFlow app.

    Inputs
    ------

    1. file: Dummy input file, use any small file. 

    Parameters
    ----------

    1. output: Output text file where "Hello World!" will be printed.

Save the file and exit the editor.

Commit the App to a Git Repo
----------------------------

Finally, commit the app to a git repo so that it can be used in a GeneFlow workflow. First, if you don't already have one, create an account in either GitHub, GitLab, BitBucket, or your company/organization's git repository. Delete the output file that was created while testing the app, since this output file is not part of the main app definition:

.. code-block:: text

    cd ~/geneflow_work/hello-world-gf2
    rm ./test/output.txt

Commit all changes to the local git repo and tag the app version: 

.. code-block:: text

    git add -A
    git commit -m "initial version of the hello world app"
    git tag 0.1

Push to the remote repo using the following commands, depending on where your repository is located.

GitHub
~~~~~~

If your repository is in GitHub, you must first create the repo on the GitHub.com site. Once created, it will likely be located at a URL similar to ``https://github.com/[user]/hello-world-gf2.git``, where ``[user]`` should be replaced with your GitHub username or group. Push your code to GitHub using the following commands:

.. code-block:: text

    git remote set-url origin https://github.com/[user]/hello-world-gf2.git
    git push --tags origin master

Be sure to replace ``[user]`` with your GitHub username or group. 

GitLab
~~~~~~

If your repository is in GitLab, you don't need to create the repo on the GitLab.com site. You can skip directly to pushing your code to the git URL, which will be similar to ``https://gitlab.com/[user]/hello-world-gf2.git``, where ``[user]`` should be replaced with your GitLab username or group:

.. code-block:: text

    git remote set-url origin https://gitlab.com/[user]/hello-world-gf2.git
    git push --tags origin master

Be sure to replace ``[user]`` with your GitLab username or group. 

Organization GitLab
~~~~~~~~~~~~~~~~~~~

If you have a company or organization GitLab server, your git repo hostname will likely be different. For example, it could be hosted at ``https://git.biotech.cdc.gov/[user]/hello-world-gf2.git``, where ``[user]`` should be replaced with your username or group:

.. code-block:: text

    git remote set-url origin https://git.biotech.cdc.gov/[user]/hello-world-gf2.git
    git push --tags origin master

Be sure to replace ``[user]`` with your organization's GitLab username or group. 

Summary
-------

Congratulations! You created a basic GeneFlow app, tested it using the auto-generated test script, and committed it to a git repo. The next tutorial covers creation of a one-step GeneFlow workflow that uses this "Hello-World" app. 
