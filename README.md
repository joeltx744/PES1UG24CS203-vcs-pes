Phase 1:
1 a)
<img width="2338" height="448" alt="_1" src="https://github.com/user-attachments/assets/e72bb100-aa84-4c74-9ded-38aec8a1beec" />
1 b)
<img width="2657" height="400" alt="_2" src="https://github.com/user-attachments/assets/1912638d-44b1-43ba-8a81-3fe363f234f1" />


Phase 2:
2 a)<img width="2369" height="448" alt="_3" src="https://github.com/user-attachments/assets/ba798ad6-8112-473f-ba60-cabadb414957" />
2 b) <img width="2369" height="448" alt="_4" src="https://github.com/user-attachments/assets/26c6c283-6b57-4813-b497-19a525f7b5fb" />

Phase 3:
3 a)<img width="1651" height="624" alt="_5" src="https://github.com/user-attachments/assets/f566c3e0-ea6f-445f-be84-bdf53cd22beb" />
3 b)<img width="2928" height="209" alt="_6" src="https://github.com/user-attachments/assets/ba81d93a-ac4d-4cf7-98ea-3f3e8cb8587c" />

Phase 4:
4 a)<img width="1453" height="736" alt="_7" src="https://github.com/user-attachments/assets/a0365616-28ed-497e-9b1c-f19c4c3c9f9d" />
4 b)<img width="1941" height="528" alt="_8" src="https://github.com/user-attachments/assets/320b5c5f-7f8e-4450-b098-2f8abb4030c2" />
4 c)<img width="2658" height="384" alt="_9" src="https://github.com/user-attachments/assets/57a0cf44-e42f-416f-a84d-b48f623adf22" />

Final Integration 
<img width="720" height="1479" alt="_10" src="https://github.com/user-attachments/assets/5d862516-e426-4255-abf8-d81ae41b6021" />

Analysis Questions
Q5.1 — Branching and Checkout
To implement pes checkout <branch>, the following changes are required:

Update .pes/HEAD to point to refs/heads/<branch>.
Read the commit hash stored in .pes/refs/heads/<branch>.
Load the corresponding commit object.
Extract the tree associated with that commit.
Update the working directory to match that tree.
Update .pes/index to reflect the new state.
The working directory must:

Add new files from the target tree
Remove files not present in the target tree
Overwrite modified files
Complexity arises because:

The working directory may contain uncommitted changes
Files may conflict between current and target branch
Partial updates must be avoided to maintain consistency
Q5.2 — Detecting Dirty Working Directory
To detect conflicts using only the index and object store:

For each tracked file in the index:

Read its stored hash (from index)
Compute the current file’s hash from disk
If hashes differ → file is modified (dirty)

Compare:

Current branch tree vs target branch tree
Identify files that differ between branches
If a file:

Is modified in working directory AND
Has different content in target branch
→ Abort checkout

This ensures no user changes are accidentally overwritten.

Q5.3 — Detached HEAD
In detached HEAD state:

.pes/HEAD contains a commit hash instead of a branch reference
New commits are created but not attached to any branch
What happens:

Commits exist but are “floating” (unreferenced)
They may be lost if garbage collection occurs
Recovery methods:

Create a new branch pointing to that commit:

pes branch <new-branch>
Or manually update a ref to that commit

Garbage Collection and Space Reclamation
Q6.1 — Finding and Deleting Unreachable Objects
Algorithm:

Start from all branch heads (.pes/refs/heads/*)

Traverse:

Commit → Tree → Blob
Mark all visited objects as reachable

Scan .pes/objects

Delete objects not in reachable set

Data Structure:

Use a hash set for efficient lookup of reachable object IDs
Estimation: For:

100,000 commits
50 branches
Worst case:

~100,000 commits
~100,000 trees
Multiple blobs per commit
Total objects visited ≈ 200,000–500,000 objects

Q6.2 — GC and Concurrency Issues
Running GC concurrently with commits is dangerous due to race conditions.

Example race condition:

Commit process creates objects (blob, tree, commit)
Before updating branch reference, GC runs
GC sees objects as unreachable (no refs yet)
GC deletes them
Commit then tries to reference deleted objects → corruption
How Git avoids this:

Uses atomic reference updates
Writes objects first, then updates refs safely
Uses locking mechanisms
GC runs only when repository is stable
Uses temporary references during operations
Conclusion
This project demonstrates the internal working of a version control system, including object storage, indexing, tree construction, commits, and repository management. It also highlights advanced concepts like branching, checkout safety, and garbage collection.



