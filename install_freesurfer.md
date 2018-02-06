# Freesurfer Install Guide

Here are the high level steps in your freesurfer install:

1. Navigate to the directory where you need to install freesurfer
2. Download freesurfer
3. Untar freesurfer
4. Set environment variables for freesurfer in your `~/.bashrc`
6. Add your freesurfer license
5. Test freesurfer

# Navigating to freesurfer install directory

To do this, use the commands `ls`, `cd`, and `mkdir` as necessary. For example, if you want to install freesurfer at the path `/software/mri`, then run the following set of commands.

```{bash}
cd /software/mri
mkdir freesurfer-6.0.0
cd freesurfer-6.0.0
```

Modify these commands as necessary so that you install freesurfer in the correct location.

This will create a new directory called freesurfer-6.0.0 in this location.

For your reference, here are some more useful Linux terminal commands

- **rmdir**: Removes a directory. Only works if the directory is empty. Example: `rmdir freesurfer-6.0.0`
- **rm**: Removes files and folders. `rm example_file.txt` will remove a file.
    - This can be used to remove non-empty directories. Example: `rm -r example_folder`
- **pwd**: Shows you the path of the current location your console is in.

# Download and Untar Freesurfer

Freesurfer is available for download on the harvard MGH website. After you go to the directory you want to install freesurfer in, you can download and untar it using the following two commands.

```
wget ftp://surfer.nmr.mgh.harvard.edu/pub/dist/freesurfer/6.0.0/freesurfer-Linux-centos6_x86_64-stable-pub-v6.0.0.tar.gz
tar -xzvf freesurfer-Linux-centos6_x86_64-stable-pub-v6.0.0.tar.gz
```

These steps may take some time, as freesurfer is quite large.

Once they are complete, your files will be extracted to a folder called `freesurfer`. Navigate into it.

```
cd freesurfer
```

# Set Freesurfer Environment Variables

In Linux, environment variables are useful because they are bits of information accessible from the Linux terminal. The `export` command sets up an environment variable.

Freesurfer requires some set-up in every terminal window. Because of this, it's a good idea to run these set-up steps from your **bashrc** file. The bashrc is a file with commands that are executed at the start-up of each new terminal window. Anything you need to do whenever a new terminal window starts should be put in your bashrc.

## Main Freesurfer Setup

To add the necessary lines to your bashrc, 
1. **make sure you are in the freesurfer install folder**
2. run the following commands

```
echo >> ~/.bashrc
echo 'export FREESURFER_HOME='`pwd` >> ~/.bashrc
echo 'source $FREESURFER_HOME/SetUpFreeSurfer.sh' >> ~/.bashrc
```

To test this, open up a new terminal window. You should see something like the following at the top:

```
-------- freesurfer-Linux-centos6_x86_64-stable-pub-v6.0.0-2beb96c --------
Setting up environment for FreeSurfer/FS-FAST (and FSL)
FREESURFER_HOME   /home/ezhao/software/freesurfer/freesurfer
FSFAST_HOME       /home/ezhao/software/freesurfer/freesurfer/fsfast
FSF_OUTPUT_FORMAT nii.gz
SUBJECTS_DIR      /home/ezhao/software/freesurfer/freesurfer/subjects
MNI_DIR           /home/ezhao/software/freesurfer/freesurfer/mni
```

If this works, then great! Move to the next step. If this is not working properly, have a look manually at your bashrc using `vi ~/.bashrc`.

## Subjects Directory

Next, set up the path to your subjects directory. I will assume that you want your subjects directory to be located at `/home/kaylees/analysis/subjects`. If you need it somewhere else, please modify the following commands before you run them.

```
mkdir -p /home/kaylees/analysis/subjects
echo 'export SUBJECTS_DIR="/home/kaylees/analysis/subjects"' >> ~/.bashrc
```

# License

The next step is to move your license to your freesurfer directory. Download the licence file from your email. It should download to `~/Downloads/license.txt`. Use the following commands to copy it (if it downloaded to a different location, find it and modify this command).

```
mv ~/Downloads/license.txt $FREESURFER_HOME/license.txt
```

Note that you can also use the Linux graphical user interface file explorer to move files around if you're more comfortable doing that.

# Test Freesurfer

Next, we'll test if freesurfer is working. If there is an error in any of these following steps, quickly look it up, and if you can't address it, let me know.

Create a test directory for testing freesurfer and go to it.

```
mkdir ~/test
cd ~/test
```

Copy sample file and convert it.

```
cp $FREESURFER_HOME/subjects/sample-001.mgz .
mri_convert sample-001.mgz sample-001.nii.gz
```

Run `ls` to check that both `sample-001.mgz` and `sample-001.nii.gz` are now there.


Run the next sample command. This creates a folder called `bert` in your subjects directory. It then runs all the steps of cortical reconstruction on the `sample-001.nii.gz` image and stores the results in `$SUBJECTS_DIR/bert`.

```
recon-all -i sample-001.nii.gz -s bert -all
```

Once all the cortical processing has completed, the output is in `$SUBJECTS_DIR/bert`. To visualize the results, use the following commands:

```
cd $SUBJECTS_DIR
freeview -v \
    bert/mri/T1.mgz \
    bert/mri/wm.mgz \
    bert/mri/brainmask.mgz \
    bert/mri/aseg.mgz:colormap=lut:opacity=0.2 \
    -f \
    bert/surf/lh.white:edgecolor=blue \
    bert/surf/lh.pial:edgecolor=red \
    bert/surf/rh.white:edgecolor=blue \
    bert/surf/rh.pial:edgecolor=red
```

If this all works, then congratulations, you have correctly installed Freesurfer!
