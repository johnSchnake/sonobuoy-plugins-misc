# Plugin via an existing image

If you have an app that already has an image, you don't have to reinvent the wheel.

Using an existing image, the only thing left to do is to instruct the image the pass the results onto the Sonobuoy worker.

For instance, the app [polaris][polaris] runs some checks and then outputs a report (it can also run a webserver).

I just run the audit, tar up the results, and report the location to the Sonobuoy worker via the done file.

```
cmd="polaris -audit > /tmp/results/resultsfile && "
cmd+="tar czf /tmp/results/results.tar.gz -C /tmp/results resultsfile && "
cmd+="echo -n /tmp/results/results.tar.gz > /tmp/results/done"

sonobuoy gen plugin \
--name=polaris \
--image quay.io/reactiveops/polaris:0.1.0 \
--cmd="/bin/sh" \
 -c="-c" \
 -c="${cmd}" \
--type Job > polaris.yaml

sonobuoy run --plugin polaris.yaml --wait
outfile=$(sonobuoy retrieve)
rm -rf results
mkdir results && tar -xf $outfile -C results 
code results
```

[sonobuoy]: https://github.com/heptio/sonobuoy
[polaris]: https://github.com/reactiveops/polaris