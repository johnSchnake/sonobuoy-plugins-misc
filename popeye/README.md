# Plugin from a github repo


## Popeye

> Note: This started as an example of how to turn a github repo for a CLI tool into a Sonobuoy plugin from start to finish. The Popeye tool now already has an published Docker image so you can (1) use this as a hypothetical example for your own workflow (2) use the [Popeye image][popeyeImage] directly with the "plugin from image" flow described [here][polaris].

I created this just as an experiment to see how simple it would be to make a [Sonobuoy][sonobuoy] plugin from [popeye][popeye].

The process is pretty simple:

 - the entire `popeye` repo is nested in this repo. Could be vendored code. Could be grabbed in some other way. It doesn't matter, but this was simple to do
 - the app needed to be built into an image, so I created a multi-stage Dockerfile which builds popeye and then puts it into an Ubuntu image. Could be a smaller image, but I wanted one that was easy to work with after the fact if I wanted to exec into it.
 - When calling the image as a plugin I needed to print the output to the `/tmp/results` (I chose a file named "output") and then inform Sonobuoy where that file is by writing the path to `/tmp/results/done`. I did that in a simple script, `run.sh`
 - Build the image with
```
$ export REGISTRY=schnake
$ docker build . -t $REGISTRY/sonobuoy-popeye:v0.1
$ docker push $REGISTRY/sonobuoy-popeye:v0.1
```
 - I had to tell Sonobuoy to run my plugin so I ran:
 ```
$ sonobuoy gen plugin --image $REGISTRY/sonobuoy-popeye:v0.1 --cmd=./run.sh --name=popeye > popeye.yaml
$ sonobuoy run --plugin popeye.yaml --wait
 ```

You can gather all the data and review it via:
```
$ outfile=$(sonobuoy retrieve)
$ mkdir results && tar -xf $outfile -C results
```

Then open the results directory with your favorite editor.

[sonobuoy]: https://github.com/heptio/sonobuoy
[popeye]: https://github.com/derailed/popeye
[popeyeImage]: https://cloud.docker.com/repository/docker/derailed/popeye
[polaris]: ../polaris