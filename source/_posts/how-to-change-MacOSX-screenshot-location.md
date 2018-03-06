title: how to change MacOSX screenshot location
tags: macosx trick
date: 2016-12-21 20:12:34
---


Taking screenshots with MacOSX is very useful when writting blogs or documents. The system has powerful built-in screenshot function. However, the default location to store the screenshot files is Desktop.

This blog will go through how to change the location of screenshots to another folder.

<!--more-->

# Step 1 Create a folder

Create a new folder anywhere using `finder` or `terminal`. Here I created `blog_statics` folder in my Google Drive folder.

![Picture](https://drive.google.com/uc?export=download&id=0ByuKQDQ-pFtiVkVNYWJsWEJHbWc)

# Step 2 Change default location of screenshots

Click the created folder in last step and press `Command+C` to copy the full path to clipboard.

Press `Command + Space` and type `terminal` to open `terminal`

In terminal, type following command:

```
defaults write com.apple.screencapture location <Folder Location>
```

Note the <Folder Location> is the location of your create folder. You could `Command+V` to paste it from clipboard

It should give following result:

![img](https://drive.google.com/uc?export=download&id=0ByuKQDQ-pFtiLUF6V2Q1alo4M3M)

Now all new screenshots should be stored in that place.

