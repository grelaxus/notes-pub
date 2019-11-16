# Installation
```sh
sudo apt-get remove youtube-dl
sudo mkdir -pv /usr/local/bin
sudo wget https://yt-dl.org/downloads/latest/youtube-dl -O /usr/local/bin/youtube-dl
sudo chmod a+rx /usr/local/bin/youtube-dl
```

# Usage 
Youtube to video-file:  
```sh
youtube-dl -f 18 <YoutubeURL>
```

Youtube to mp3 file:  
```sh
youtube-dl --extract-audio --audio-format mp3 
```

