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
  cat /results/guidellm-llama-benchmark-20260226T212734Z.json \
  > ./guidellm-llama-benchmark-20260226T212734Z.json

oc -n demo-guidellm exec pvc-reader -- \
  cat /results/tokenizers/tokenizer.json > ./tokenizer.json

oc -n demo-guidellm exec pvc-reader -- \
  cat /results/tokenizers/tokenizer_config.json > ./tokenizer_config.json
```


To write tokenizer files to the pod:

```
# Get the waiting job pod name
POD=$(oc -n demo-guidellm get pods -l job-name=guidellm-llama-benchmark-job-pvc -o jsonpath='{.items[0].metadata.name}')
echo "$POD"

Upload local tokenizer files directly into PVC root via waiting pod
oc -n demo-guidellm exec -i "$POD" -- /bin/sh -c 'cat > /pvc/tokenizer.json' < tokenizer.json
oc -n demo-guidellm exec -i "$POD" -- /bin/sh -c 'cat > /pvc/tokenizer_config.json' < tokenizer_config.json

```