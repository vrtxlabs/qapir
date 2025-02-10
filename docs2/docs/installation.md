---
slug: /installation
sidebar_position: 2
---

# Installation

## Step 1: Download `Qapir` 
Go to [Qapir's official GitHub page](https://github.com/vrtxlabs/qapir/releases/latest) and choose a build with the architecture that matches your setup.

**Linux amd64**

```
wget https://github.com/vrtxlabs/qapir/releases/latest/download/qapir-linux-amd64.tgz
```

**Mac arm64**

```
curl -LO https://github.com/vrtxlabs/qapir/releases/latest/download/qapir-darwin-arm64.tgz
```

## Step 2: Unzip the archive

**Linux amd64**

```
sudo tar -zxvf qapir-linux-amd64.tgz -C /opt/
```

**Mac arm64**

```
sudo tar -zxvf qapir-darwin-arm64.tgz -C /opt/
```

## Step 3: Create a symlink to make `qapir` globally-available

```
sudo ln -s /opt/qapir /usr/local/bin/qapir
```

## Step 4: (Optional) Delete the archive

**Linux amd64**

```
rm -rf qapir-linux-amd64.tgz
```

**Mac arm64**

```
rm -rf qapir-darwin-arm64.tgz
``` 
