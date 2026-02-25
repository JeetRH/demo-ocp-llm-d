---
Edit the gm-job.yaml file with the correct route to the model and the correct image to pull the tokenizers

Then run the following command:
```
oc apply -k gitops/instance/guidellm/guidellm-job
```


---
After the job completes, create a temporary pvc-reader pod. Use **pvc-reader.yaml**

After that execute the following command to get the json file your local machine (Replace file names):

```
oc -n demo-guidellm exec pvc-reader -- \
  cat /results/guidellm-llama-benchmark-20260225T191656Z.json \
  > ./guidellm-llama-benchmark-20260225T191656Z.json
```