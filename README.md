# Torrent Downloader using Google Colab

This script facilitates downloading torrents directly to your Google Drive using Google Colab. It employs `libtorrent` to handle torrent downloads and requires minimal setup.

## Getting Started

### Prerequisites

- Access to Google Colab (https://colab.research.google.com/)
- Google Drive storage for downloading torrents
- Basic understanding of using Colab notebooks

### Installation

1. Mount Google Drive to the notebook environment by running the provided code block:

    ```python
    from google.colab import drive
    drive.mount('/content/drive')
    ```

2. Install dependencies by executing the following commands:

    ```bash
    !python -m pip install --upgrade pip setuptools wheel
    !python -m pip install lbry-libtorrent
    ```

## Usage

1. Execute the script in a Colab notebook environment.
2. When prompted, paste the torrent/magnet link into the input field.
3. The script will start downloading the torrent to the specified location in your Google Drive.

## Example

```python
# Mount Google Drive
from google.colab import drive
drive.mount('/content/drive')

# Install dependencies
!python -m pip install --upgrade pip setuptools wheel
!python -m pip install lbry-libtorrent

# Capture magnet link from the user
link = input("PASTE TORRENT/MAGNET LINK HERE \n")

# Code to download torrent
import libtorrent as lt
import time
import datetime

ses = lt.session()
ses.listen_on(6881, 6891)
params = {
    'save_path': '/content/drive/My Drive/Torrent/',
    'storage_mode': lt.storage_mode_t(2)
}

handle = lt.add_magnet_uri(ses, link, params)
ses.start_dht()

begin = time.time()
print(datetime.datetime.now())

print('Downloading Metadata...')
while not handle.has_metadata():
    time.sleep(1)
print('Got Metadata, Starting Torrent Download...')

print("Starting", handle.name())

while handle.status().state != lt.torrent_status.seeding:
    s = handle.status()
    state_str = ['queued', 'checking', 'downloading metadata', 'downloading', 'finished', 'seeding', 'allocating']
    print('%.2f%% complete (down: %.1f kb/s up: %.1f kB/s peers: %d) %s ' %
          (s.progress * 100, s.download_rate / 1000, s.upload_rate / 1000, s.num_peers, state_str[s.state]))
    time.sleep(5)

end = time.time()
print(handle.name(), "COMPLETE")

print("Elapsed Time: ", int((end - begin) // 60), "min :", int((end - begin) % 60), "sec")

print(datetime.datetime.now())
```

## Notes

- Make sure you have sufficient space in your Google Drive for downloading large files.
- This script runs in the Colab environment, so ensure continuous internet connectivity during the download process.
