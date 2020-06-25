# How to create the kubernetes the hard way screencasts

Open the first terminal window.

```none
# Clone this repo:
git clone git@github.com:my-own-kind/kubernetes-the-hard-way-in-containers.git

# change into the kthw-docker directory:
cd kubernetes-the-hard-way-in-containers/kthw-docker

# Install the cmdline-player command:
sudo npm i -g command-line-player
```

Open another terminal window and make it large and white.

Open another 100x38 window, this time black, and place on top of the white one.

In the topmost black terminal, which will be recorded:

```none
while true; do screen -r -e ^Oo screencast; sleep .5; done
```

In the first teminal, start the screencast recording. It will take about 50 mins.

```none
# Click the window to record after entering the next command
wname=$(cmdline-player -q)

# Start recording
for i in {2..14}; do cmdline-player -w "$wname" -c kthw/kthw-$i.md screencasts/kthw-$i.scr <<<"\n"; sleep 1; done
```

The KTHW markdown format 'labs' will be created in the 'kthw' directory. Transcripts will also be placed in the 'kthw' directory. Video files will be placed in the 'images' directory.

That's it.
