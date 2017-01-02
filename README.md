This repo showcases a bug in Git LFS on case-insensitive filesystems (such as
Windows and most macOS).

## Reproduce

To reproduce this bug, just follow the commits in this repository. But
basically the steps are:

1. Create a new repository without Git LFS
2. On a case-insensitive filesystem (such as Windows or a default HFS partition
on macOS), commit two files (*even in different directories!*) whose filenames
match, except for the case of the extension. For example, in this repository,
there is `img/a/1.jpg` and `img/b/1.JPG`.
3. Enable Git LFS for the repository, and start tracking one or both of the
extensions. In this repository, I added ```*.jpg filter=lfs diff=lfs merge=lfs -text```
to `.gitattributes`.
4. Push and then try to re-clone the repository on a case-insensitive
filesystem. The clone will hang forever. For example, cloning this repository
on Windows or macOS results in:

```
$ git clone git@github.com:apetresc/git-lfs-bug
Cloning into 'git-lfs-bug'...
remote: Counting objects: 16, done.
remote: Compressing objects: 100% (11/11), done.
remote: Total 16 (delta 0), reused 16 (delta 0), pack-reused 0
Receiving objects: 100% (16/16), 383.15 KiB | 0 bytes/s, done.
Downloading img/a/1.jpg (33.38 KB)
```
followed by a permanent hang that must be ended with `SIGTERM`. *However,
cloning on a case-sensitive filesystem like Linux will work just fine!*


Obviously this is a breaking bug that acts as a dealbreaker for using Git LFS
if Windows/macOS needs to be supported. I haven't gotten as far as a diagnosis
yet, I am hoping someone on the Git LFS team will be able to figure it out on
sight without me having to dig into LFS internals :)
