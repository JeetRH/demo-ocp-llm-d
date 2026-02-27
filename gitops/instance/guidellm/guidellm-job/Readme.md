---
Edit the gm-gptoss-pvc-job.yaml file with the correct route to the model

Then run the following command:
```
oc apply -k gitops/instance/guidellm/guidellm-job
```
For Windows cmd:
```
oc apply -k demo-ocp-llm-d\gitops\instance\guidellm\guidellm-job
```

---
The job starts running and waits for the tokenizer files to be uploaded to the pod. Use the following commands
```
oc -n demo-guidellm exec -i "$POD" -- /bin/sh -c 'cat > /pvc/tokenizer.json' < tokenizer.json
```
```
oc -n demo-guidellm exec -i "$POD" -- /bin/sh -c 'cat > /pvc/tokenizer_config.json' < tokenizer_config.json
```

For Windows cmd:

```
type tokenizer.json | oc -n demo-guidellm exec -i "$POD"  -- sh -c "cat > /pvc/tokenizer.json
```
```
type tokenizer_config.json | oc -n demo-guidellm exec -i "$POD"  -- sh -c "cat > /pvc/tokenizer_config.json
```

After the guidellm tests are complete, the pod will wait for 15 mins so that we can copy the results file (Replace file name)

```
oc -n demo-guidellm exec "$POD" -- cat /results/guidellm-gptoss-benchmark.json > ./guidellm-gptoss-benchmark.json

```

---
Extra:

Copy tokenizer and results from pod to local:

```
oc -n demo-guidellm exec "$POD" -- ls -l /results/tokenizers
```
 
```
oc -n demo-guidellm exec "$POD" -- cat /results/tokenizers/tokenizer.json > tokenizer.json
```
 
```
oc -n demo-guidellm exec "$POD" -- cat /results/tokenizers/tokenizer_config.json > tokenizer_config.json
```
 
```
oc -n demo-guidellm "$POD" -- cat /results/guidellm-benchmark-20260227T174824Z.json > guidellm-benchmark-20260227T174824Z.json
```