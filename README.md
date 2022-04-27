# FreeFuzz How To

## Getting Started

### 1. Requirements

1. Install MongoDB following https://www.mongodb.com/docs/manual/installation/
2. FreeFuzz uses Python >= 3.8.0, so ensure that you have the correct Python version
3. Install the required library using `pip install -r requirements.txt`
4. Then, load the database dump that is located in the `dump/` folder. Run the command:
```shell
mongorestore dump/
```

### 2. Configuration file

- Configuration file is located in the `src/config/` folder. For my run, I typically use the `src/config/demo_tf.conf` 
- In this configuration file, change the value of `each_api_run_times=1` to `each_api_run_times = 1000`. This will make the demo run similar to the actual run of FreeFuzz
- The `src/config/skip_tf.txt` contains a list of skipped tensorflow API as they may cause crash during FreeFuzz execution. I did not change this list during my run.

### 3. Running FreeFuzz

After finishing the configuration and requirements steps, we can run FreeFuzz:

```shell
cd src && python FreeFuzz.py --conf demo_tf.conf
```

### 4. Run Outputs

The run output will be produced in the `tf-output/` folder. There will be several folders and files within this folder:
- `crash-oracle/` contains the output from the crash oracle. I usually use the test cases produced in the `crash-oracle/success/` folder.
- `cuda-oracle/` contains the output from the cuda oracle.
- `precision-oracle/` contains the output from the precision oracle.
- `runerror.txt` contains the list of APIs that are error during the run. It will also list the command that causes the error, which is very helpful for us to debug the error.
- `timeout.txt` list of APIs that got timeout during the run.





### 5. Collected Outputs

- At this point, I have run FreeFuzz for Tensorflow 10 times for the list of 385 APIs given by Kang Hong Jin. 
- I modified the FreeFuzz.py code at lines 59-62 to make it retrieve the list of API name from the text file rather than from the database.
- However, FreeFuzz requires the API to have mutation entries in the database and will result in error during the run due to the API having no mutation. This will result in `returned non-zero exit status 1.` in the `runerror.txt`.
- The testcases produced from the 10 runs is available in the following link:
https://drive.google.com/file/d/1QMp4j5j3jLqvG7-Z9f3l8a1HySAgLL2U/view?usp=sharing.
Do note that instead of test cases for the 385 APIs, it only has test cases from approximately 85 APIs due to the aforementioned error.


### 6. Collecting Data by Yourself

I have not tried this approach. I assume that this is required to instruments new API so that it can be used with FreeFuzz.

1. Go to `src/instrumentation/{torch, tensorflow}` to see how to intrument the dynamic information and add them into the database
2. After adding invocation data, you should run the following command to preprocess the data for PyTorch

```shell
cd src && python preprocess/process_data.py torch
```

or for TensorFlow
```shell
cd src && python preprocess/process_data.py tf
```