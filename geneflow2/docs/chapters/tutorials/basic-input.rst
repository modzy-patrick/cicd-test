.. basic-input

Basic Workflow and App Inputs
=============================

This tutorial extends the previously created "Hello World" workflow and app to process a single input file. The "One-Step Workflow: Hello World" tutorial must be completed, and its resulting "Hello World!" workflow should be committed to a Git repo prior to beginning this tutorial.

Modify the "Hello World!" App
-----------------------------

Clone the previous "Hello World!" app from the Git repo (be sure to replace Git repo URL with the appropriate URL for your app):

.. code-block:: text

    cd ~/geneflow_work
    git clone https://github.com/[USER]/hello-world-gf2.git

Note: if the ``hello-world-gf2`` directory still exists with the contents of the previous app tutorial, do a ``git pull`` instead:

.. code-block:: text

    cd ~/geneflow_work/hello-world-gf2
    git pull

Define an App Input
~~~~~~~~~~~~~~~~~~~

Update the app metadata to reflect the new functionality:

.. code-block:: text

    vi ./app.yaml

Change the ``description`` field to indicate a change in the app; add a ``git`` field (with [USER] replaced with your username, and github.com replaced with the appropriate Git service); update the ``version`` field to ``0.2``:

.. code-block:: yaml

    # Basic app information

    # name: standard GeneFlow app name
    name: hello-world-gf
    # description: short description for the app
    description: Updated hello world GeneFlow app
    # git: link to the app's git repo
    git: https://github.com/[USER]/hello-world-gf2.git
    # version: must be incremented every time this file, or any file in the app
    # project is modified
    version: '0.2'

Next, update the ``file`` input in the ``inputs`` section of the ``app.yaml`` file. Change the ``label`` and ``description`` of the ``file`` input to ``Input text file``. Change the ``required`` field to ``true`` and add a ``test_value`` field with value ``${SCRIPT_DIR}/data/file.txt``. The ``test_value`` field is only used when running the app's test script. We will define the ``/data/file.txt`` file in the next section. ``${SCRIPT_DIR}`` in the context of the test script is the directory in which the test script is located. The updated ``inputs`` section of the ``app.yaml`` file should look like:

.. code-block:: yaml

    inputs:
      file:
        label: Input Text File
        description: Input text file
        type: File
        required: true
        test_value: ${SCRIPT_DIR}/data/file.txt

Finally, change the execution commands of the app in the ``exec_methods`` section. 

.. code-block:: yaml

    exec_methods:
    - name: environment
      if:
      - in_path: 'cat'
      exec:
      - run: cat ${FILE_FULL}
        stdout: ${OUTPUT_FULL}

The modified execution block first checks if the ``cat`` command is available (it should be available in all standard Linux systems). It then runs the ``cat`` command to print the contents of the file passed as the ``file`` input. ``${FILE_FULL}`` is a Bash variable that is automatically defined for the ``file`` input, and represents the full path of the ``file`` input. 

Save and close the ``app.yaml`` file.

Add a Test File to the App
~~~~~~~~~~~~~~~~~~~~~~~~~~

Previously, we added a file called ``file.txt`` to the test folder. Modify this file so that it contains the string ``Hello World File Contents!``:

.. code-block:: text

    cd ~/geneflow_work/hello-world-gf2/test/data
    vi ./file.txt

Make sure the file has the contents:

.. code-block:: text

    Hello World File Contents!

Save and close the ``file.txt`` file.

Re-"Make" the App
~~~~~~~~~~~~~~~~~

Now that the ``app.yaml`` file has been updated and the test file defined. Rebuild the app with the following commands:

.. code-block:: text

    cd ~/geneflow_work/hello-world-gf2
    geneflow make-app .

This should re-generate the ``agave-app-def.json.j2``, ``hello-world-gf2.sh``, and ``test.sh`` scripts. 

Test the App
~~~~~~~~~~~~

Run the ``test.sh`` script to test the app:

.. code-block:: text

    cd ~/geneflow_work/hello-world-gf2/test
    ./test.sh

You should see output similar to the following:

.. code-block:: text

    CMD=/home/[USER]/geneflow_work/hello-world-gf/test/../assets/hello-world-gf.sh --file="/home/[USER]/geneflow_work/hello-world-gf/test/data/file.txt" --output="output.txt" --exec_method="auto"
    File: /home/[USER]/geneflow_work/hello-world-gf/test/data/file.txt
    Output: output.txt
    Execution Method: auto
    Detected Execution Method: environment
    CMD=cat /home/[USER]/geneflow_work/hello-world-gf/test/data/file.txt  >"/home/[USER]/geneflow_work/hello-world-gf/test/output.txt"
    Exit code: 0
    Exit code: 0

The ``output.txt`` file should have been created in the test directory with the text ``Hello World File Contents!``. View it with:

.. code-block:: text

    cat ./output.txt

And you should see:

.. code-block:: text

    Hello World File Contents!

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

    Version: 0.2

    This is a basic GeneFlow app with an input.

    Inputs
    ------

    1. file: Input text file.

    Parameters
    ----------

    1. output: Output text file where "Hello World File Contents!" will be printed.

Commit and Tag the New App
~~~~~~~~~~~~~~~~~~~~~~~~~~

Finally, commit the updated app to the Git repo and update its tag to reflect the new version number:

.. code-block:: text

    cd ~/geneflow_work/hello-world-gf2
    git add -u
    git commit -m "update app input"
    git tag 0.2
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

Update the workflow metadata by incrementing the version number to ``0.2``:

.. code-block:: text

    vi ./workflow.yaml

The metadata section should have the line:

.. code-block:: yaml

    version: '0.2'

Update Apps
~~~~~~~~~~~

Update the "Apps" section by incrementing the version:

.. code-block:: yaml

    apps:
      hello-world:
        git: https://github.com/[USERS]/hello-world-gf2.git
        version: '0.2'

Be sure to replace the "git" field with the correct Git repo to which you committed the "Hello World!" app.

Define a Workflow Input
~~~~~~~~~~~~~~~~~~~~~~~

Update the ``inputs`` section of ``workflow.yaml`` so that the ``file`` input label and description are correct:

.. code-block:: yaml

    inputs:
      file:
        label: Input Text File
        description: Input text file
        type: File
        enable: true
        visible: true

Pass the Workflow Input to the App
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``steps`` section of ``workflow.yaml`` should already be defined to take the correct workflow input:

.. code-block:: yaml

    steps:
      hello:
        app: hello-world
        depend: []
        template:
          file: ${workflow->file}
          output: output.txt

Save and close the ``workflow.yaml`` file.

Update the Workflow README
~~~~~~~~~~~~~~~~~~~~~~~~~~

Update the ``README.rst`` file to reflect the new workflow input:

.. code-block:: text

    cd ~/geneflow_work/hello-world-workflow-gf2
    vi ./README.rst

Modify the file so it looks like the following:

.. code-block:: text

    Hello World! One-Step GeneFlow Workflow
    =======================================

    Version: 0.2

    This is a basic one-step GeneFlow workflow that prints the contexts of an input text file to an output text file.

    Inputs
    ------

    1. file: Input text file.

    Parameters
    ----------

    None

Commit and Tag the New Workflow
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Commit the new version of the workflow to the Git repo:

.. code-block:: text

    cd ~/geneflow_work/hello-world-workflow-gf2
    git add -u
    git commit -m "updated hello world workflow"
    git tag 0.2
    git push --tags origin master

Install and Test the Workflow
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Now that the workflow has been updated and committed to a Git repo, we can install and test it:

.. code-block:: text

    cd ~/geneflow_work
    geneflow install-workflow -g https://github.com/[USER]/hello-world-workflow-gf2.git -c --make_apps ./test-workflow-0.2

This command installs the updated "Hello World!" one-step workflow, and its updated "Hello World!" app into the directory ``test-workflow-0.2``. Remember to replace the Git URL with the URL to which you committed the workflow.

Test the workflow to validate its functionality:

.. code-block:: text

    geneflow run ./test-workflow-2.0 -o output --in.file=./test-workflow-0.2/data/test.txt

This command runs the workflow in the ``test-workflow-0.2`` directory using the test data and copies the output to the ``output`` directory.

Once complete, you should see a file called ``output.txt`` with the same text as the input ``test.txt`` file, ``Test Hello World!``:

.. code-block:: text

    cat ./output/geneflow-job-[JOB ID]/hello/output.txt

Be sure to replace ``[JOB ID]`` with the ID of the GeneFlow job. The job ID is a randomly generated string and ensures that workflow jobs do not overwrite existing job output. You should see the following text in the ``output.txt`` file:

.. code-block:: text

    Test Hello World!

Summary
-------

Congratulations! You updated the one-step GeneFlow workflow so that it can handle a basic input file. The next tutorial will expand on this workflow by using an app with piped execution.
