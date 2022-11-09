# Simple NextFlow Templates for Developing your own Workflows
These templates can be used to format any new workflow written in Nextflow's DSL2 language and version control it with Git.

## The Workflow ".nf" Script
A workflow file, `main.nf`, is provided with empty code for the following:
1. Specifying that the computer should interpret the script with the Nextflow language, and further specifying that it should use the more powerful second version of that language
2. Space for the workflow specification at the top, complete with comments delimiting channel specifications and workflow steps.
3. Space for what I call "derivative parameters," which are additional parameters set in the workflow file that are derived from parameters set in `nextflow.config`/in the command line. In the past, I have often used derivative parameters to create context-specific subfolders of the results/ folder, among other things.
4. Space for process specifications, complete with most of the options you may wish to use, including:
	- a comment at the top describing what the process does
	- a tag, which can be used to label each execution of the process
	- `publishDir`, which tells Nextflow where to place output files and how to get them there (I most often use copy, but the default is to symlink)
	- sample memory, cpu, and time directives that should be changed or removed in most cases
	- input and output lines, which must match those specified above in the workflow specification; the `when` directive, which can be used with groovy code or pattern matching to specify when a process should execute (e.g., when certain conditions are met, like when a file name contains the name of a certain gene of interest)
	- And of course, the script, where you place code in BASH, by default, or in any other language when followed by a matching shebang (e.g. `#!/usr/bin/env python3` for python 3). In most cases, the script code must be surrounded by triple double quote marks. I recommend that you copy-paste the process template as many times as needed to flesh out your workflow.

## The Configuration File
The configuration file, `nextflow.config`, is where you specify parameters for each run. Note that all the settings specified in the `params { }` section can be accessed as double-dash flags in the command line. In the case of these templates, and example would be specifying a different results folder outside its default in the workflow folder, like so: `nextflow run main.nf --results /absolute/path/to/where/you/want/results/placed`. Note that this folder will be created if it doesn't already exist. In all, the configuration file includes blank templates for:
1. Workflow parameters, with comments delimiting space for parameters that should be reviewed at each run versus parameters that should usually be left unchanged, at their defaults.
2. Whether to generate an HTML-formatted report of run statistics. This is useful when the workflow is mostly production ready and you are at most fine-tuning, but is often unnecessary when debugging or building a workflow. By default, I set it to on.
3. Whether to generate a png-formatted visualization of the workflow, analogous to a directed acyclic graph (DAG). Note that to create a PNG, you need [GraphViz](https://graphviz.org/download/) installed on your system. Alternatively, you can switch it to PDF format or HTML format by using `file = 'workflow-visualization.pdf'` or `file = 'workflow-visualization.html'` respectively.
4. Whether to remove all files from the `work/` subdirectories to save disk space. This only executes when a workflow finishes successfully, in which case output files will theoretically have been copied or move to a results folder. Note, however, that if you wish to resume the workflow with the `-resume` flag in the command-line, `cleanup = true` will make this impossible. For this reason, I set it to false by default, but include it here to bring awareness to it.
5. Profiles! Profiles can be set in the command line and used to bring in software dependencies with different tools, e.g., `-profile conda`. By default, if no profiles are specified in the command line, the workflow uses the profile labeled `standard`, which in this case uses Docker. I have also included process settings, where you can specify containers for each process by name rather than running the whole workflow in one container (or, alternatively, in one conda environment). As long as you have docker, conda, or singularity installed, Nextflow will handle pulling and running itself.
6. A manifest, which is mostly useful when pulling a workflow directly from GitHub with NextFlow (rather than using `git clone` to do so first. The manifest specifies:
	- a homepage, which is a url to wherever a website for the workflow may be. In the past, I simply paste the github repo here.
	- the main script to use when pulling from GitHub. By default, NextFlow looks for `main.nf`, but I specfiy this here explicitly to make users aware of this option. You can name your workflow file anything as long as you specify the name in the manifest.
	- a default version control branch. Without specifying a branch in the manifest or in the command line, nextflow will not know where to get the workflow code on GitHub (or wherever else)
	- a short description of the workflow
	- the author of the workflow

## A Git Ignore file
The file `.gitignore` tells git which folders and files to ignore. In most cases, you will want to refrain from uploading the following to GitHub:
1. The nextflow cache folder, which is called `.nextflow`.
2. The work directory `work/`. Most of the files within this directory are actually symlinks anyway, which cannot be uploaded to GitHub or elsewhere. As such, the `work/` directory is generally useless outside the context of whatever machine the workflow was run on, and will often contain large intermediate files, depending on your use case.
	- _Important sidenote about this work directory_: Within `work/`, Nextflow creates a sub-directory that serves as a working directory for each and every execution of every process. This is why we don't have to worry about file paths in NextFlow in the same way we worry about them in Snakemake, say: As long as you properly specify inputs and outputs, the script and each process will always be able to find the files it needs in the current directory, `.`. This is great for scientific reproducibility as well, as it means you never need to hard-code file paths anywhere (unless you're specifying them as a parameter in `nextflow.config`.
3. The Nova cache directory for users who, like me, use the app Nova to write scripts, manage projects, and transfer files.
4. Nextflow logs (`nextflow.log`), which are generally useless outside the context of the machine a workflow is run on.
5. Old trace files, reports, and timelines that are now deprecated.
6. Intermediate `.dot` files for the DAG visualization. This is an archaic file format that is produced when you try to produce a DAG png (see above), but do not have GraphViz installed.
7. Old DAG PNG's, which are deprecated.

## Learning NextFlow
With that, you know what you need to get started using these templates. For more resources for learning NextFlow, I recommend any of the following fine sites:
- [nf-core's byte-size resources for NextFlow learners](https://nf-co.re/events/2022/bytesize-resources-to-learn-nextflow)
- [NextFlow Developer Sateesh Peri's Tutorial](https://sateeshperi.github.io/nextflow_varcal/nextflow/)
- [NBI Sweden Reproducibility Workshop GitHub](https://github.com/NBISweden/workshop-reproducible-research/tree/main/pages/nextflow) - This one is more up to date than the version on canva, which is the next link
- [NBI Sweden Reproducibility Workshop Canva](https://uppsala.instructure.com/courses/51980/pages/nextflow-1-introduction?module_item_id=328997)
- [NextFlow Official Documentation](https://www.nextflow.io/docs/latest/index.html)
- [Exhaustive NextFlow Github.io Tutorial](https://nextflow-io.github.io/patterns/index.html#_basic_patterns)
- [Seqera Labs NextFlow Tutorial](https://training.seqera.io/)
- [Tronflow NextFlow tutorial](https://tronflow-docs.readthedocs.io/en/latest/02_tutorial.html)
- [Google Cloud NextFlow tutorial](https://cloud.google.com/life-sciences/docs/tutorials/nextflow)
- [Data Carpentries Incubator Workshop on NextFlow](https://carpentries-incubator.github.io/workflows-nextflow/index.html) - This one is especially thorough and helpful, and also introduces nf-core.
- [NextFlow Cheatsheet PDF](https://github.com/danrlu/Nextflow_cheatsheet/blob/main/nextflow_cheatsheet.pdf) - Not a tutorial, but this is a useful PDF to have on hand.
