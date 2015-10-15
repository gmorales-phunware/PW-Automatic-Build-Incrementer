# Automatic Build Incremented

## Steps: 
1. Select your target and go to Build Phases
2. Add a new Run Script and rename to Increment Build Number
3. Paste the following script:

```
#!/bin/bash
buildNumber=$(/usr/libexec/PlistBuddy -c "Print CFBundleVersion" "$INFOPLIST_FILE")
buildNumber=$(($buildNumber + 1))
/usr/libexec/PlistBuddy -c "Set :CFBundleVersion $buildNumber" "$INFOPLIST_FILE"

```

## Automatic Version Increment
```
versionNumber=$(/usr/libexec/PlistBuddy -c "Print CFBundleShortVersionString" "$INFOPLIST_FILE")
versionNumber=$(($versionNumber + 1))
/usr/libexec/PlistBuddy -c "Set :CFBundleShortVersionString $versionNumber" "$INFOPLIST_FILE"
```

4. Run and stop worrying about updating the build number!

## Solution # 2
```
git=`sh /etc/profile; which git`
branch_name=`$git symbolic-ref HEAD | sed -e 's,.*/\\(.*\\),\\1,'`
git_count=`$git rev-list $branch_name |wc -l | sed 's/^ *//;s/ *$//'`
simple_branch_name=`$git rev-parse --abbrev-ref HEAD`

build_number="$git_count"
if [ $CONFIGURATION != "Release" ]; then
    build_number+="-$simple_branch_name"
fi

plist="${TARGET_BUILD_DIR}/${INFOPLIST_PATH}"
dsym_plist="${DWARF_DSYM_FOLDER_PATH}/${DWARF_DSYM_FILE_NAME}/Contents/Info.plist"

/usr/libexec/PlistBuddy -c "Set :CFBundleVersion $build_number" "$plist"
if [ -f "$DSYM_INFO_PLIST" ] ; then
    /usr/libexec/PlistBuddy -c "Set :CFBundleVersion $build_number" "$dsym_plist"
fi
```