all: prepare debug

prepare: activate cleanrns getrns

clean:
	@echo Cleaning...
	-(rm -r ./__pycache__)
	-(rm -r ./app_storage)
	-(rm -r ./share/pkg/*)
	-(rm -r ./share/mirrors/*)
	-(rm -r ./bin)

cleanlibs:
	buildozer android clean

cleanall: clean cleanlibs

activate:
	(. venv/bin/activate)

pacthfiles: patchsdl injectxml

patchsdl:
	# Pach USB HID behaviour
	cp patches/HIDDeviceUSB.java .buildozer/android/platform/build-arm64-v8a_armeabi-v7a/build/bootstrap_builds/sdl2/jni/SDL/android-project/app/src/main/java/org/libsdl/app/HIDDeviceUSB.java
	cp patches/HIDDeviceUSB.java .buildozer/android/platform/build-arm64-v8a_armeabi-v7a/dists/sideband/src/main/java/org/libsdl/app/HIDDeviceUSB.java
	cp patches/HIDDeviceUSB.java .buildozer/android/platform/build-arm64-v8a_armeabi-v7a/dists/sideband/jni/SDL/android-project/app/src/main/java/org/libsdl/app/HIDDeviceUSB.java

	# Pach service loader
	cp patches/PythonService.java .buildozer/android/platform/python-for-android/pythonforandroid/bootstraps/common/build/src/main/java/org/kivy/android/PythonService.java
	cp patches/PythonService.java .buildozer/android/platform/build-arm64-v8a_armeabi-v7a/build/bootstrap_builds/sdl2/src/main/java/org/kivy/android/PythonService.java
	cp patches/PythonService.java .buildozer/android/platform/build-arm64-v8a_armeabi-v7a/dists/sideband/src/main/java/org/kivy/android/PythonService.java

injectxml:
	# mkdir /home/markqvist/.local/lib/python3.11/site-packages/pythonforandroid/bootstraps/sdl2/build/src/main/xml
	# Inject XML on arm64-v8a
	mkdir -p .buildozer/android/platform/build-arm64-v8a_armeabi-v7a/dists/sideband/src/main/res/xml
	mkdir -p .buildozer/android/platform/build-arm64-v8a_armeabi-v7a/dists/sideband/templates
	cp patches/device_filter.xml .buildozer/android/platform/build-arm64-v8a_armeabi-v7a/dists/sideband/src/main/res/xml/
	cp patches/file_paths.xml .buildozer/android/platform/build-arm64-v8a_armeabi-v7a/dists/sideband/src/main/res/xml/
	cp patches/AndroidManifest.tmpl.xml .buildozer/android/platform/build-arm64-v8a_armeabi-v7a/dists/sideband/templates/
	cp patches/p4a_build.py .buildozer/android/platform/build-arm64-v8a_armeabi-v7a/dists/sideband/build.py

debug:
	buildozer android debug

prebake:
ifneq (,$(wildcard .buildozer/android/platform/build-arm64-v8a_armeabi-v7a/dists/sideband/src/main/res/xml/device_filter.xml))
	@echo Device filter XML exists, skipping prebake
else
	@(echo Prebaking build before patching files...)
	@(sleep 2)
	-(buildozer android release)
	@(echo Prebake finished, applying patches and rebuilding...)
	@(sleep 2)
endif

fetchshare:
	rm ./share/pkg/*
	rm -r ./share/mirrors/*
	cp ../../dist_archive/rns-*-py3-none-any.whl ./share/pkg/
	cp ../../dist_archive/rnspure-*-py3-none-any.whl ./share/pkg/
	cp ../../dist_archive/lxmf-*-py3-none-any.whl ./share/pkg/
	cp ../../dist_archive/nomadnet-*-py3-none-any.whl ./share/pkg/
	cp ../../dist_archive/rnsh-*-py3-none-any.whl ./share/pkg/
	cp ../../dist_archive/RNode_Firmware_*_Source.zip ./share/pkg/
	cp -r ../../dist_archive/reticulum.network ./share/mirrors/
	cp -r ../../dist_archive/unsigned.io ./share/mirrors/
	cp ../../dist_archive/Reticulum\ Manual.pdf ./share/mirrors/Reticulum_Manual.pdf
	cp ../../dist_archive/Reticulum\ Manual.epub ./share/mirrors/Reticulum_Manual.epub

release:
	buildozer android release

postbuild:
	$(MAKE) cleanrns

apk: prepare prebake pacthfiles fetchshare release postbuild

devapk: prepare prebake pacthfiles fetchshare debug postbuild

version:
	@(echo $$(python ./gv.py))

install:
	adb install bin/sideband-$$(python ./gv.py)-arm64-v8a_armeabi-v7a-release.apk

console:
	(adb logcat | grep "python\|sidebandservice")

getrns:
	cp -rv ../../Reticulum/RNS ./
	-(rm -r ./RNS/Utilities/RNS)
	-(rm -r ./RNS/__pycache__)
	cp -rv ../../LXMF/LXMF ./
	-(rm -r ./LXMF/Utilities/LXMF)
	-(rm -r ./LXMF/__pycache__)

cleanrns:
	-(rm -r ./RNS)
	-(rm -r ./LXMF)
