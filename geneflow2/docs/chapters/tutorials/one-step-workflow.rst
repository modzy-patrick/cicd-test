.. one-step-workflow

One-Step Workflow: Hello World
==============================

This tutorial covers the creation of a basic one-step GeneFlow workflow that uses the "Hello World!" app created in the previous tutorial. The "Basic App: Hello World" tutorial must be completed, and its resulting "Hello World!" app should be committed to a Git repo prior to beginning this tutorial.

Clone the GeneFlow Workflow Template
------------------------------------

Enter the previously created "geneflow_work" directory:

.. code-block:: text

    cd ~/geneflow_work

Make sure GeneFlow is still available in environment either by loading the Python virtual environment, or loading the module (refer to the "Basic App: Hello World" tutorial for instructions).

Clone the workflow template from the GeneFlow public workflows repository:

.. code-block:: text

    git clone https://gitlab.com/geneflow/workflows/workflow-template-gf2.git hello-world-workflow-gf2

This command downloads the workflow template into the "hello-world-workflow-gf2" directory, which also happens to be the name of the workflow for this tutorial. View the contents of the app template using the "tree" command:

.. code-block:: text

    cd hello-world-workflow-gf2
    tree .

You should see the workflow template directory structure (it may have some extra files):

.. code-block:: text

    .
    ├── data
    │   └── README.rst
    ├── README.rst
    └── workflow.yaml

    3 directories, 5 files

When creating a workflow, the "workflow.yaml" file must be modified. It is also recommended to edit the main "README.rst" file, and add test data to the "data" directory. 

Configure the Workflow
----------------------

The "workflow.yaml" file contains the workflow definition. Edit each section of the file to create the "Hello World" one-step workflow.

Metadata
~~~~~~~~

Edit each field of the metadata as follows:

name:
  This is the name of the workflow and should be a short string. Use "Hello World Workflow".

description:
  This is a short description of the workflow, which should be limited to one sentence. Use "Hello World one-step workflow"

git:
  This is a link to the workflow's Git repo. You may either leave this field blank, or use the URL of the Git repo to which you intend to commit the workflow. E.g., https://github.com/[USER]/hello-world-workflow-gf2.git.

version:
  This is the version number of the workflow. We recommended to use a low version number, e.g., 0.1, if this is the first version of a workflow. In this example, use '0.1'. The version number must be quoted to ensure that it is a string. 

username:
  This is the author of the workflow. Use either your name or just "user".

Once complete, the metadata section of the "workflow.yaml" should look similar to:

.. code-block:: yaml

    # metadata
    name: Hello World Workflow
    description: Hello World one-step workflow
    git: https://github.com/[USER]/hello-world-workflow-gf2.git
    version: '0.1'
    username: user

Be sure to replace the "git" field with your specific Git repo.

Apps
~~~~

The "Apps" section specifies all GeneFlow apps that the workflow uses. Edit this section so that the list includes only one app, the "Hello World!" app created in the previous tutorial:

.. code-block:: yaml

    apps:
      hello-world:
        git: https://github.com/[USER]/hello-world-gf2.git
        version: '0.1'

When editing this section, be sure to delete any other apps in the file so that the "Hello World!" app is the only app listed. Also be sure to replace the "git" field with the correct Git repo to which you committed the "Hello World!" app.

Final Output
~~~~~~~~~~~~

The "Final Output" section of the workflow definition simply lists all steps for which output should be copied to the workflow's final output directory. This is useful for workflows with a large number of intermediate steps generating intermediate output that may not be of interest to workflow runners. This example workflow only contains one step, so we will list that step in the final output section:

.. code-block:: yaml

    final_output:
    - hello

"hello" is the name of the step that we'll define in the "steps" section. 

Inputs and Parameters
~~~~~~~~~~~~~~~~~~~~~

Inputs are files or folders that are passed to GeneFlow apps. Parameters are strings or numerical values passed to GeneFlow apps. The "Hello World!" app requires a single "dummy" input file, so we will define a single input for the workflow called "file":

.. code-block:: yaml

    # inputs
    inputs:
      file:
        label: Dummy Input File
        description: Dummy input file
        type: File
        enable: true
        visible: true

No parameters are required for this workflow, so leave that section blank:

.. code-block:: yaml

    # parameters
    parameters:

Steps
~~~~~

The "steps" section of the workflow definition defines all workflow steps and their order of execution. This workflow only has one step and no dependencies. Use the following definition for the "steps" section:

.. code-block:: yaml

    # steps
    steps:
      hello:
        app: hello-world
        depend: []
        template:
          file: ${workflow->file}
          output: output.txt

The "app" field points to the app defined in the "apps" section of the workflow definition. The blank "depend" list indicates that this step does not depend on any other steps. The "template" section defines the values passed to the "Hello World!" app inputs and parameters. ``${workflow->file}`` refers to the input "file" passed to the workflow. Thus, the "file" input passed to the workflow is passed to the "file" input of the "Hello World!" app.

Save and close the "workflow.yaml" file. 

Add Test Data
-------------

Add a single file to the "data" directory for testing the workflow. Since this is a "dummy" input file, the file contents do not really matter:

.. code-block:: text

    echo "Test Hello World!" > ./data/test.txt

Update the Workflow README
--------------------------

It is best practice to update the workflow README file to include the workflow name, a short description, and descriptions for each input and parameter. Edit the README.rst file in the main workflow directory:

.. code-block:: text

    cd ~/geneflow_work/hello-world-workflow-gf2
    vi ./README.rst

Modify the file so it looks like the following:

.. code-block:: text

    Hello World! One-Step GeneFlow Workflow
    =======================================

    Version: 0.1

    This is a basic one-step GeneFlow workflow that prints "Hello World!" to a text file.

    Inputs
    ------

    1. file: Dummy input file, use any small file.

    Parameters
    ----------

    None

Commit the Workflow to a Git Repo
---------------------------------

We'll use GitHub as an example, but you may use GitLab, BitBucket, or your company/organization's Git repo instead. GitHub requires you to first create the repo on the GitHub.com site. Once created, it will likely be located at a URL similar to https://github.com/[user]/hello-world-workflow-gf2.git, where [user] should be replaced with your GitHub username or group. If you're using a Git repo other than GitHub, refer to the instructions in the "Basic App: Hello World" tutorial.

Before committing the workflow code, remove the "apps" directory, since this directory is created during workflow installation.

.. code-block:: text

    cd ~/geneflow_work/hello-world-workflow-gf2
    rm -rf ./apps

Push the code to GitHub using the following commands: 

.. code-block:: text

    git add -A
    git commit -m "initial version of the hello world workflow"
    git tag 0.1
    git remote set-url origin https://github.com/[USER]/hello-world-workflow-gf2.git
    git push --tags origin master

Be sure to replace ``[USER]`` with your GitHub user or group.

Install the Workflow from a Git Repo
------------------------------------

Now that the workflow has been committed to a Git repo, it can be installed anywhere:

.. code-block:: text

    cd ~/geneflow_work
    geneflow install-workflow -g https://github.com/[USER]/hello-world-workflow-gf2.git -c --make-apps ./test-workflow

This command installs the "Hello World!" one-step workflow, and its "Hello World!" app into the directory "test-workflow". Remember to replace the Git URL with the URL to which you committed the workflow.

Test the Workflow
-----------------

Finally, test the workflow to validate its functionality:

.. code-block:: text

    geneflow run ./test-workflow -o output --in.file=./test-workflow/data/test.txt

This command runs the workflow in the "test-workflow" directory using the test data and copies the output to the "output" directory.

Once complete, you should see a file called "output.txt" with the text "Hello World!":

.. code-block:: text

    cat ./output/geneflow-job-[JOB ID]/hello/output.txt

Be sure to replace ``[JOB ID]`` with the ID of the GeneFlow job. The job ID is a randomly generated string and ensures that workflow jobs do not overwrite existing job output. You should see the following text in the "output.txt" file:

.. code-block:: text

    Hello World!

Summary
-------

Congratulations! You created a one-step GeneFlow workflow, committed it to a Git repo and, and tested it. The next tutorial will expand on this workflow by adding a more complex workflow input. 
