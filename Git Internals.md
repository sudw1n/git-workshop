---

---
# Git Internals
+ look at git as a content-addressable file-system; key-value store
+ expect less pragmatism, sorry
+ goal: understand how git works and get better appreciation for good engineering
+ initially a toolkit rather than a VCS
+ plumbing vs porcelain
---
## Reference
https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain
### Also See
https://www.youtube.com/watch?v=2sjqTHE0zok
---
## Concepts
+ history as a directed acyclic graph
+ fast forward
+ delta compression
--
+ describe contents of `.git/`
+ `objects`: all contents of our repo
+ object types: blobs, trees, commits, tags
+ `refs`: pointers into commit objects (branches, tags, remotes, etc.)
+ `HEAD`: pointer to current commit
+ `index`: staging area info
---
## Blobs
- git can store any type of content
- plumbing command: `git hash-object`
- to examine content: `git cat-file`
--
### exercise
version controlling with plumbing commands
1. create a file and store it in object database
2. make changes to file and store it again
3. delete the file
4. retrieve the previous versions of the file
---
## Trees
+ contains references (hashes) to blobs or other trees
+ store a filename and group files together
+ explain `main^{tree}` syntax
--
+ git creates a tree by writing tree objects from staging area
+ to create a tree: setup the index
+ plumbing command: `git update-index`
+ to write index out to a tree object: `git write-tree`
--
### exercise
+ create a file and make a tree object out of it; save it as `TREE_VERSION_1`
+ make changes to the file and add a new file; create a tree `TREE_VERSION_2`
+ add `TREE_VERSION_1` as sub-directory to `TREE_VERSION_2` using `git read-tree`
+ store the latest tree as `TREE_VERSION_3`
---
## Commits
+ problem with above exercise: need to track SHA-1 values of each tree object
+ better to remember one hash which refers to all three versions; introduce redirection
+ also want to remember who saved the snapshots, why, what time, date, etc.
+ create a commit object with `git commit-tree`
--
### Exercise
- build git history without using porcelain commands (`add` and `commit`)
- create commits to model history of the three trees
- `VERSION_1` <= `VERSION_2` <= `VERSION_3`

---
## Object Storage
+ objects stored as `type + len + content`, compressed
---
### manually creating a blob
```python
import hashlib
import zlib
import os

sha1 = hashlib.sha1()

content = "hello world"
header = f"blob {len(content)}\0"
# blob object to store
store = (header + content).encode("utf-8")

sha1.update(store)
# hash of blob
hash = sha1.hexdigest()

# blob compressed
compressed = zlib.compress(store)

# where to store blob
directory = f".git/objects/%s/" % hash[:2]
file_name = hash[2:]
path = directory + file_name

# create the directory before-hand
if not os.path.exists(directory):
	os.makedirs(directory)

# write blob to
with open(path, "wb") as f:
	f.write(compressed)
```
---
## Git References
+ problem: still need to remember hash of commit
+ easier to store the hash in a file under a name
+ references: `git/refs/`
+ add a hash to `.git/refs/heads/main`
+ use `git update-ref`
+ `git branch` => `git update-ref`
+ `HEAD`: save hash of last commit
---
## Tags
+ similar to commit object: contains author, message, pointer
+ difference: points to commit object rather than tree
+ similar to branch but never moves
+ create lightweight tag with: `git update-ref`
+ create an annoted tag with `git tag -a`
+ tag stores reference to an `object` which can be of any type
--
### exercise
- store an object (e.g., gpg public key) as a tag
---
## Remotes
- git stores value last pushed to remote at `refs/remotes`
- remotes are read-only but branches are not
---
## Packfiles
+ git can compress files with `zlib` scheme
+ `git gc`
+ stored at `objects/info/packs`
+ `git verify-pack -v <idx>`
---
## RefSpec
+ adding a remote adds a section to `.git/config`
+ format: `<+src>:<dest>`
+ `+`: update reference even if not fast-forward
+ `src`: pattern for ref on remote
+ `dest`: pattern for ref on local
+ can be specified for `fetch` and `push`
+ default: fetches all references in remote to local
--
+ can use refspec for comands like `git log`
+ `git push origin :main`: delete `main` on remote
---
# The END