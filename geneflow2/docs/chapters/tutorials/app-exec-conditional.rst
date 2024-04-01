.. app-exec-conditional

Conditional Execution in Apps
=============================

This tutorial extends the previously created "Hello World" workflow and app to demonstrate conditional execution in apps. The "Piped Execution in Apps" tutorial must be completed, and its resulting "Hello World!" workflow should be committed to a Git repo prior to beginning this tutorial.

Modify the "Hello World!" App
-----------------------------

Clone the previous "Hello World!" app from the Git repo (be sure to replace the Git repo URL with the appropriate URL for your app):

.. code-block:: text

    cd ~/geneflow_work
    git clone https://github.com/[USER]/hello-world-gf2.git

Note: if the ``hello-world-gf2`` directory still exists with the contents of the previous app tutorial, do a ``git pull`` instead:

.. code-block:: text

    cd ~/geneflow_work/hello-world-gf2
    git pull

Update App Metadata
~~~~~~~~~~~~~~~~~~~

Update the app metadata to reflect the new functionality:

.. code-block:: text

    vi ./app.yaml

Change the ``description`` field to indicate a change in the app; leave the ``git`` field as is (e.g., with [USER] replaced with your username, and github.com replaced with the appropriate Git service); update the ``version`` field to ``0.4``:

.. code-block:: yaml

    # Basic app information

    # name: standard GeneFlow app name
    name: hello-world-gf
    # description: short description for the app
    description: Updated hello world GeneFlow app demonstrating conditional execution
    # git: link to the app's git repo
    git: https://github.com/[USER]/hello-world-gf2.git
    # version: must be incremented every time this file, or any file in the app
    # project is modified
    version: '0.4'

Update App Parameters
~~~~~~~~~~~~~~~~~~~~~

Add a string parameter named ``mode`` to the app to control the app's execution. If this parameter is set to ``basic``, then the app will simply output ``Hello World!`` to a file. However, if this parameter is set to any other value, the app will execute as defined in the previous tutorial, i.e., it will use pipes to count the number of words in the input file. Leave the ``output`` parameter as is.

.. code-block:: yaml

    parameters:
      mode:
        label: Mode
        description: Execution mode
        type: string
        required: false
        default: basic
        test_value: basic
      output: 
        label: Output Text File
        description: Output text file
        type: File
        required: true
        test_value: output.txt

Update App Execution Commands
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Update the app execution commands in the ``exec_methods`` section so that it checks the ``mode`` parameter and executes commands accordingly:

.. code-block:: yaml

    exec_methods:
    - name: environment
      if:
      - in_path: cat
      - in_path: wc
      - in_path: echo
      exec:
      - if:
        - str_equal: ['${MODE}', 'basic']
        run: echo 'Hello World!'
        stdout: ${OUTPUT_FULL}
        else:
        - pipe:
          - run: cat ${FILE_FULL}
          - run: wc -w
            stdout: ${OUTPUT_FULL}

The modified ``exec_methods`` block first checks if three command-line utilities are available in the system path: ``echo``, ``cat`` (which prints the contents of a file) and ``wc`` (which counts the number of words). If two or more items are defined in the ``if`` block, they are treated as an ``AND`` conditional, so ``echo``, ``cat``, and ``wc`` must all be available in the system path in order for the ``environment`` execution method to run. The ``if`` block checks the value of the ``mode`` parameter. If ``mode`` is set to ``basic``, the app echos ``Hello World!`` to the output file, as in the original version of the "Hello World!" app. However, if the ``mode`` parameter is set to anything else, the app executes the piped command as defined in the previous "Piped Execution in Apps" tutorial. 

Re-"Make" the App
~~~~~~~~~~~~~~~~~

Now that the ``app.yaml`` file has been updated with the new conditional execution block and a new parameter has been added, rebuild the app with the following commands:

.. code-block:: text

    cd ~/geneflow_work/hello-world-gf2
    geneflow make-app .

This should re-generate the ``agave-app-def.json.j2``, ``hello-world-gf2.sh``, and ``test.sh`` scripts. 

Test the App
~~~~~~~~~~~~

Run the ``test.sh`` script to test the app. Note: this test uses the same test input file added in the previous tutorial:

.. code-block:: text

    cd ~/geneflow_work/hello-world-gf2/test
    ./test.sh

You should see output similar to the following:

.. code-block:: text

    CMD=/home/[USER]/geneflow_work/hello-world-gf/test/../assets/hello-world-gf.sh --file="/home/[USER]/geneflow_work/hello-world-gf/test/data/file.txt" --mode="basic" --output="output.txt" --exec_method="auto"
    File: /home/[USER]/geneflow_work/hello-world-gf/test/data/file.txt
    Mode: basic
    Output: output.txt
    Execution Method: auto
    Detected Execution Method: environment
    CMD=echo 'Hello World!'  >"/scicomp/home/ktr2/geneflow_work/hello-world-gf/test/output.txt"
    Exit code: 0
    Exit code: 0

The ``output.txt`` file should have been created in the test directory with the text ``Hello World!``. View it with:

.. code-block:: text

    cat ./output.txt

And you should see the appropriate text:

.. code-block:: text

    Hello World!

When we test the workflow, we'll execute it using the "advanced" condition.

Update the App README
~~~~~~~~~~~~~~~~~~~~~

Update the app ``README.rst`` file to reflect changes to the app:

.. code-block:: text

    cd ~/geneflow_work/hello-world-gf2
    vi ./README.rst

Modify the file so it looks like the following:

.. code-block:: text

    Hello World! Updated GeneFlow App
    =================================

    Version: 0.4

    This is a basic GeneFlow app that demostrates piping and conditional execution.

    Inputs
    ------

    1. file: Input text file.

    Parameters
    ----------

    1. mode: Mode of execution to test app conditions. 'basic' mode will pipe 'Hello World!' to a text file. Any value other than 'basic' will process the input file with a series of commands that counts the number of words in the input file. 

    2. output: Output text file where either 'Hello World!', or number of words in the input text file will be printed.

Commit and Tag the New App
~~~~~~~~~~~~~~~~~~~~~~~~~~

Finally, commit the updated app to the Git repo and update its tag to reflect the new version number:

.. code-block:: text

    cd ~/geneflow_work/hello-world-gf2
    git add -u
    git commit -m "update hello world app with conditional execution"
    git tag 0.4
    git push --tags origin master

Modify the "Hello World!" Workflow
----------------------------------

Now that the app has been updated, we can update the "Hello World!" workflow to use the new app.

Checkout the previous "Hello World!" workflow:

.. code-block:: text

    cd ~/geneflow_work
    git clone https://github.com/[USER]/hello-world-workflow-gf2.git

Note: if the ``hello-world-workflow-gf2`` directory still exists with the contents of the previous workflow tutorial, do a ``git pull`` instead:

.. code-block:: text

    cd ~/geneflow_work/hello-world-workflow-gf2
    git pull

Update Workflow Metadata
~~~~~~~~~~~~~~~~~~~~~~~~

Update the workflow metadata by incrementing the version number to ``0.4``:

.. code-block:: text

    vi ./workflow.yaml

The metadata section should have the line:

.. code-block:: yaml

    version: '0.4'

Update Apps
~~~~~~~~~~~

Update the "Apps" section by incrementing the version:

.. code-block:: yaml

    apps:
      hello-world:
        git: https://github.com/[USER]/hello-world-gf2.git
        version: '0.4'

Be sure to replace the "git" field with the correct Git repo to which you committed the "Hello World!" app.

Add a Workflow Parameter
~~~~~~~~~~~~~~~~~~~~~~~~

In the ``workflow.yaml`` file, edit the ``parameters`` section so that it includes a ``mode`` parameter:

.. code-block:: yaml

    parameters:
      mode:
        label: Mode
        description: Execution mode
        type: string
        default: basic
        enable: true
        visible: true

This parameter will be passed on to the new app in the ``steps`` section.

Update Workflow Steps
~~~~~~~~~~~~~~~~~~~~~

Update the ``steps`` section so that the ``hello`` step passes the new ``mode`` parameter to the app:

.. code-block:: yaml

    steps:
       hello:
         app: hello-world
         depend: []
         template:
           file: ${workflow->file}
           mode: ${workflow->mode}
           output: output.txt

Update the Workflow README
~~~~~~~~~~~~~~~~~~~~~~~~~~

Update the ``README.rst`` file to reflect the new functionality:

.. code-block:: text

    cd ~/geneflow_work/hello-world-workflow-gf2
    vi ./README.rst

Modify the file so it looks like the following:

.. code-block:: text

    Hello World! One-Step GeneFlow Workflow
    =======================================

    Version: 0.4

    This is a basic one-step GeneFlow workflow that demonstrates piping and conditional execution.

    Inputs
    ------

    1. file: Input text file.

    Parameters
    ----------

    1. mode: Mode of exeuction. 'basic' mopde will pipe 'Hello World!' to a text file. Any value other than 'basic' will process the input file with a series of commands that counts the number of words in the input file.

Commit and Tag the New Workflow
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Commit the new version of the workflow to the Git repo:

.. code-block:: text

    cd ~/geneflow_work/hello-world-workflow-gf2
    git add -u
    git commit -m "updated hello world workflow"
    git tag 0.4
    git push --tags origin master

Install and Test the Workflow
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Now that the workflow has been updated and committed to a Git repo, we can install and test it:

.. code-block:: text

    cd ~/geneflow_work
    geneflow install-workflow -g https://github.com/[USER]/hello-world-workflow-gf2.git -c --make_apps ./test-workflow-0.4

This command installs the updated "Hello World!" one-step workflow, and its updated "Hello World!" app into the directory ``test-workflow-0.4``. Remember to replace the Git URL with the URL to which you committed the workflow.

Test the workflow to validate its functionality:

.. code-block:: text

    geneflow run ./test-workflow-0.4 -o output --in.file=./test-workflow-0.4/data/test.txt --param.mode=basic

This command runs the workflow in the ``test-workflow-0.4`` directory using the test data, ``mode`` of "basic", and copies the output to the ``output`` directory.

Once complete, you should see a file called ``output.txt`` with contents of ``Hello World!``:

.. code-block:: text

    cat ./output/geneflow-job-[JOB ID]/hello/output.txt

Be sure to replace ``[JOB ID]`` with the ID of the GeneFlow job. The job ID is a randomly generated string and ensures that workflow jobs do not overwrite existing job output. You should see the following text in the ``output.txt`` file:

.. code-block:: text

    Hello World!

Next, test the workflow with an alternate mode (i.e., other than "basic"):

.. code-block:: text

    geneflow run ./test-workflow-0.4 -o output --in.file=./test-workflow-0.4/data/test.txt --param.mode=advanced

This command runs the workflow in the ``test-workflow-0.4`` directory using the test data, ``mode`` of "advanced", and copies the output to the ``output`` directory.

Once complete, you should see a file called ``output.txt`` with contents of ``4``: 

.. code-block:: text

    cat ./output/geneflow-job-[JOB ID]/hello/output.txt

Be sure to replace ``[JOB ID]`` with the ID of the GeneFlow job. You should see the following text in the ``output.txt`` file:

.. code-block:: text

    3

Summary
-------

Congratulations! You updated the one-step GeneFlow workflow so that it demonstrates conditional execution in the app. The next tutorial will expand on this workflow by adding more steps.
