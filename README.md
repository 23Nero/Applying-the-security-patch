
# Applying the security patche




#### 2. How to check your security patch level on the flashed devices? 

```bash
  adb shell getprop ro.vendor.build.security_patch
```

#### 3. What build type will be used for Security Testing? 

```bash
  userdebug
```
#### 4. How do we get the failure log while running STS?

```bash
  adb logcat
```

- To automatically collect some logs on failure, you can add the following option to your Tradefed command line:

```bash
  --auto-collect LOGCAT_ON_FAILURE
  or
  --auto-collect SCREENSHOT_ON_FAILURE
```

- For convenience, logcat and screenshot each have a direct flag:

```bash
  --logcat-on-failure
  and
  --screenshot-on-failure
```

reference:

https://source.android.com/docs/core/tests/tradefed/testing/through-tf/log-on-failure

#### 5. Let’s imagine you have 2 kinds of devices (such as SX5, M20). How do you run STS on those devices in parallel? 

```bash
  run [test_plan] --shard-count=X -s SERIAL1 -s SERIALX
```
Example:

```bash
  run [test_plan] --shard-count=2 -s SERIAL_SX5 -s SERIAL_M20
```

reference: 

https://source.android.com/docs/compatibility/cts/run

https://source.android.com/docs/core/tests/tradefed/architecture/xml-config

#### 6. How do you run a single module? A single test case? A specific architecture (e.g arm-v8) 

- Run a module:
```bash
  run [testplan] [-s SERIAL] -m module_name
```

- Run a single testcase:
```bash
  run [testplan] [-s SERIAL] -m module_name -t testcase
```

reference: https://source.android.com/docs/core/tests/development/atest



#### 7. Your colleague shared a STS result that has some failures. To retry from that result, what do you do? 

- Reboot, factory reset, config device again: https://source.android.com/docs/compatibility/cts/setup#device-config.
- Review the STS result, analyze log result to determine which tests failed. 
- Fix failed test and run single test failed.
- Monitor the log run, and debug if failed again.

#### 8. How to run STS on a device which is different from Security Patch Level? 

- STS is designed to test specific Security Patch Levels. 
- If the device’s SPL does not match the SPL that STS expects, issues or incomplete results happen.

#### 9. What do you do if STS/BTS reports a CVE failure that has already been applied? 
- Check the Patch Level
- Analyze ID fail in **host_log_xxx.txt**
- Reboot -> factory reset -> config device.
- Run the singel test failed
- Analyze log failed if fail again.

#### 10. Create a script to apply a single CVE to your source code with the input: security patch and Android source code?
```bash

#the python script is being completed

import os
import subprocess

PATCH_FILE = '/media/lamle/HDD/2024-08-20240809T075818Z-001/2024-08/2024-08-android-bulletin-partner-preview-patches/patches/android-13.0.0_r1/platform'
SOURCE_CODE_DIR = '/media/lamle/HDD/sx5_hdd/LINUX/android'
ISSUE_MAPPING_DIR = '/media/lamle/HDD/2024-08-20240809T075818Z-001/2024-08/2024-08-android-bulletin-partner-preview-patches/patches/issue-mapping'

def find_patch_file(cve_id):
    for root, dirs, files in os.walk(ISSUE_MAPPING_DIR):
        for file in files:
            if cve_id in file:
                return os.path.join(root, file)
    return None

def apply_patch(patch_file, source_code_dir):
    if not os.path.isfile(patch_file):
        print(f"Error patch dir")
        return False

    if not os.path.isdir(source_code_dir):
        print(f"Error source dir")

        return False
    os.chdir(source_code_dir)

    try:
        result = subprocess.run(['git', 'apply', '-p1', patch_file], check=True, text=True, capture_output=True)
        print("apply patch success")
        print(result.stdout)
        return True
    except subprocess.CalledProcessError as e:
        print("erro apply")
        print(e.stderr)
        return False

def main():
    cve_id = input("Issue mapping ID: ").strip()

    patch_file = find_patch_file(cve_id)
    
    if patch_file:
        if apply_patch(patch_file, SOURCE_CODE_DIR):
            print("Done apply")
        else:
            print("Cant apply")
    else:
        print("Not see apply")

if __name__ == "__main__":
    main()

```



#### 11. Based on the test result without html file, how to read? 

- In folder lastest/logs/*, we can cat and grep to see CVE fail.
```bash
  cat event-logs_xxx.txt | grep FAIL
```
- Then trace that fail ID in **test_result.xml** to know generic information of fail
- Know more iformation of fail, we can trace in **device_log_cat_test** file.


#### 12. When running the test, which 2 folders are created and their uses?
- **Results**: this folder contains the results of the test, including reports and results information.
- **Logs**: this directory contains detailed log files about the test process, helping you track and analyze events and errors.

#### 13. How to check the number of retries when I have results given by others?

- We can use "l r" to check ID number.
- Use simple python script to read **result/test_result.xml** file, to read **invocation-id="XX"**

```bash
# python file created in android-sts folder
import xml.etree.ElementTree as ET

xml_file = './results/latest/test_result.xml'

tree = ET.parse(xml_file)
root = tree.getroot()

for build in root.findall('Build'):
    invocation_id = build.get('invocation-id')
    
    print(f"Invocation ID: {invocation_id}")
    print(f"Number of retries: {int(invocation_id) -1 }  ")
```

#### 14. Meaning of files in log folder?

- **device_logcat_setup_xxx.txt**: contain logcat from device during setup phase before starting test.

- **device_logcat_teardown_xxx.txt**: contain logcat from device during end of test phase.

- **device_logcat_test_xxx.txt**: contain logcat  from the device while the tests are being executed. This is the main log, containing detailed information about the activities and events that occur during the test.

- **event-logs_xxx.txt**: contain test-related events, such as system or application events.

- **executeShellCommandLog_xxx.txt**: contain  shell commands executed on the device during testing.

- **filter-arm64-_xxx.txt**: contain  filters or excludes specific tests or test cases.

- **host_adb_log_xxx.txt**: contain  logs from adb on the host.

- **host_log_xxx.txt**: contain logs from the test host. Provides information about activities and events on the host during the test.

- **suite-exclude-filters_xxx.txt**: contain exclusion filters for the test suite.

- **tradefed-expanded-config_xxx.xml**: contain the Tradefed expanded configuration.




