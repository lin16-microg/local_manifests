# How to build
General assumption is, that you are familiar with building ROMs and how to use git etc.
The [LineageOS build instructions (example: gts210vewifi device)](https://wiki.lineageos.org/devices/gts210vewifi/build) should provide you with needed additional informations. 

## Purpose of this organization
The main purpose of this organization is to build the "hardened microG" variants for below listed devices:
- HUAWEI P9 EVA (treble build)
- Motorola Moto G3 (osprey) - not updated any longer, see https://github.com/lin17-microG
- OnePlus 3T (oneplus3) - not updated any longer, see https://github.com/lin17-microG
- Samsung Galaxy Tab S2 LTE exynos (gts210ltexx)
- Samsung Galaxy Tab S2 WiFi 2016 (gts210vewifi)
- Sony Xperia Z1 compact (amami) - not updated any longer, see https://github.com/lin17-microG

## Setup of build environment
### Step 1 - Initialize the build tree
Create the directory, which should contain your build tree and 'cd' into it and execute the below command sequence
```Shell session
repo init -u https://github.com/LineageOS/android.git -b lineage-16.0 --groups=all,-notdefault,-darwin,-mips
cd .repo
git clone https://github.com/lin16-microG/local_manifests 
cd local_manifests 
git checkout lineage-16.0
cd ../.. 
repo sync --no-tags
```

### Step 2 - Finalize tree setup
Copy and execute the scripts as shown below. 

**IMPORTANT:** `repo sync` performs a checkout without branch (detached state). 
The below scripts perform a 'real' checkout and will create the branch to be checked out. 
```Shell session
cp z_patches/root-scripts/* .
./switch_microG.sh reference
./switch_microG.sh default
./switch_microG.sh microG
./switch_microG.sh treble
./switch_microG.sh reference
```

### Step 3 - Adapt your preferences
If you want to **sign** your build with your own signing keys, make sure that the directoty `~/.android-certs` exists and contains your signing keys.
You have in the 'root' of your build tree a collection of shell scripts of the type build_*devicename*.sh, e.g. build_gts210vewifi.sh for the gts210vewifi device.
Edit the script of your choice to define your own ccache directory and (optionally) your build output directory (comment out, if you would like to use 
the default location `out/` within your build tree).

### Step 4 - Checkout the correct branches and build
- If you want to build a 'default LineageOS 16.0' ROM (possible for all devices, except the HUAWEI P9), checkout the correct branches with the command
`./switch_microG.sh default`
- If you want to build the 'hardened microG' variant (possible for all devices, except the HUAWEI P9), checkout the correct branches with the command
`./switch_microG.sh microG`F
- If you would like to build the 'hardened microG' variant for the HUAWEI P9 (as it's a treble build, it should work for other devices as well), 
checkout the correct branches with the command `./switch_microG.sh treble`
- To build without signing (to be more precise: sign with the publicly known test keys), execute `./build_*device*.sh test` (e.g. `./build_hotdog.sh test`)
- To build using your own signing key (which must be present in `~/.android-certs` !), execute `./build_*device*.sh sign` (e.g. `./build_hotdog.sh sign`)
- For the HUAWEI P9, the build command is `./build_treble.sh sign|test [root]` (example: signed build without baked in root: `./build_treble.sh sign`, 
signed build with baked in root: `./build_treble.sh sign root`) 
- To build the android emulator, execute `./build_emulator.sh test` and to start it, execute `./start_emulator.sh`

### IMPORTANT: How to re-synch the build tree
Always execute `./switch_microG.sh reference`, before you perform another `repo sync` to synchronize your tree.
After having synchronized (you may have to perform a --force-sync, if further forks have been created), check whether the switch script has been updated in directory `z_patches/croot-scripts` and copy it into the 'root' of your build tree. Afterwards, switch to the specific build variant. 
