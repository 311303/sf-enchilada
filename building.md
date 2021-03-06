> Note, the SDK required for a specific command will be expressed as follows:

* HOST: `[HOST]`

* PLATFORM_SDK: `[PLATFORM]`

* HABUILD_SDK: `[HABUILD]`

# First Time Setup

* Create the file `~/.hadk.env` with [this contents](files/.hadk.env)
* Create the file `~/.mersdk.profile` with [this contents](files/.mersdk.profile)
* Create the file `~/.mersdubu.profile` with [this contents](files/.mersdkubu.profile)

Download and install `repo`

`[HOST]`
```sh
mkdir ~/bin
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod +x ~/bin/repo
```

Then add this to your `~/.bashrc`
```sh
export PATH="$HOME/bin:$PATH"
```

Now follow the [OnePlus 5 initial building guide](https://github.com/sailfishos-oneplus5/important/blob/master/INITIAL-BUILDING.md#setup-the-platform-sdk) from `setup the platform sdk`, it's safe to ignore all OnePlus 5 specific information.

> Thanks [@deathmist](https://github.com/JamiKettunen) ;)


# From source to device

## First time

### Setup source tree

`[HOST]`
```sh
cd $ANDROID_ROOT
repo init -u git://github.com/mer-hybris/android.git -b hybris-16.0 --depth 1
git clone https://github.com/sailfish-oneplus6/local_manifests .repo/local_manifests
```

### Sync all the required packages

`[HABUILD]`
```sh
repo sync -c -j`nproc` --fetch-submodules --no-clone-bundle --no-tags
# Hybris-16.0 specific patches, not in the HADK yet
hybris-patches/apply-patches.sh --mb
```

`[HABUILD]`
```sh
mka hybris-hal
```

`[HABUILD]`
```sh
echo "MINIMEDIA_AUDIOPOLICYSERVICE_ENABLE := 1" > external/droidmedia/env.mk
sed "s/Werror/Werror -Wno-unused-parameter/" -i frameworks/av/services/camera/libcameraservice/Android.mk
mka droidmedia audioflingerglue
```

`[PLATFORM]`
```sh
bp -gg
```

## Every build

Build android parts (boot image + device parts)

`[HABUILD]`
```sh
mka hybris-hal
```

Copy the fstab to out so that mount units for /system and /vendor get generated properly

```sh
cp device/oneplus/sdm845-common/rootdir/etc/fstab.qcom out/target/product/enchilada/root/
```

Build Sailfish packages and generate a rootfs

`[PLATFORM]`
```sh
bp
```

## Tips

### When/what to build a new image

#### Configs

If only modifying sparse files it is safe to only run:
```sh
bp -cvi
```
This builds `droid-configs-enchilda`, `droid-version-enchilada` and then generates a new image.

#### Android (HAL) parts

If modifying fstab or any `.rc` files, run
```sh
bp -dvi
```
This builds `droid-hal-enchilda`, `droid-version-enchilada` and then generates a new image.


#### Most of the time, you don't need to rebuild everything.
