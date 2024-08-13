
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

To automatically collect some logs on failure, you can add the following option to your Tradefed command line:

```bash
--auto-collect LOGCAT_ON_FAILURE
or
--auto-collect SCREENSHOT_ON_FAILURE

```

For convenience, logcat and screenshot each have a direct flag:

```bash
--logcat-on-failure
and
--screenshot-on-failure

```

reference:

https://source.android.com/docs/core/tests/tradefed/testing/through-tf/log-on-failure

#### 5. Let’s imagine you have 2 kinds of devices (such as SX5, M20). How do you run STS on those devices in parallel? 

```bash

```
reference: 

https://source.android.com/docs/compatibility/cts/run

https://source.android.com/docs/core/tests/tradefed/architecture/xml-config

#### 6. How do you run a single module? A single test case? A specific architecture (e.g arm-v8) 

```bash
    atest test-to-run [optional-arguments]
```
reference: https://source.android.com/docs/core/tests/development/atest



#### 7. Your colleague shared a STS result that has some failures. To retry from that result, what do you do? 

- Review the STS result, analyze log result to determine which tests failed. 
- Fix failed test and run single test failed.
- Monitor the log run, and debug if failed again.

#### 8. How to run STS on a device which is different from Security Patch Level? 

- STS is designed to test specific Security Patch Levels. 
- If the device’s SPL does not match the SPL that STS expects, issues or incomplete results happen.

#### 9. What do you do if STS/BTS reports a CVE failure that has already been applied? 
- Check the Patch Level
- Run the singel test failed
- Analyze log failed

#### 10. Create a script to apply a single CVE to your source code with the input: security patch and Android source code?

