Ai log:

prompt 1: i want to do #9(sequence diagram). what do i need to do first? (+ image with tasks)

Answer: Before you start #9, you need #6 Operation contracts and #7 CRC/responsibility model. Both still look open on your board.

Design sequence diagrams take their inputs from the other artefacts:

What #9 needs	Where it comes from	Status on your board
Which system operations to diagram (usually one diagram per operation)	#5 System sequence diagrams	Closed ✓
What each operation must achieve (objects created, associations formed, attributes changed)	#6 Operation contracts	Open
Which object handles each message, and who it works with	#7 CRC/responsibility model	Open
The classes and methods the diagrams must match	#8 Design class diagram	Closed ✓

Suggested order:

Finish #6. For each system operation in your SSDs, write the contract: the operation, cross-references, preconditions and postconditions. Postconditions are the key part. Each one ("a Booking instance was created", "Booking was linked to Equipment", and so on) becomes something a sequence diagram has to show.
Finish #7. Decide who is responsible for what, ideally with GRASP reasoning: Controller, Creator, Information Expert, Low Coupling. For example, which class receives the system event, and which class creates the Booking? Your sequence diagrams will just draw these decisions out.
Start #9. Pick one system operation, such as the main scenario from #1. Start from the controller receiving the message. Follow your CRC decisions until every postcondition in the contract is met.

comment: Here i asked AI to help me with context. It explained to me in which files all needed data are and i saved a lot of time on searching. 

prompt 2: I have a problem. I started editing my code, but I haven't pulled the latest update from GitHub. How do I fix that?

answer: Don't worry, your changes won't be lost. The safest way is to save your work in a commit first, then bring in the updates.

Step 1: check where you are
bash
git status
The first line shows your branch, e.g. On branch use-case-catalogue.
Below that are the files you changed.

If it says On branch main, stop and send me the output. You shouldn't commit directly to main.

Step 2: commit your changes
bash
git add .
git commit -m "WIP: update section 5.1 and traceability"

WIP means "work in progress". If .DS_Store appears in the list, add your files by name instead of ., e.g. git add ca1-analysis/report/CA2-analysis.md.

Step 3: get the latest updates
bash
git fetch origin
git merge origin/main
fetch downloads the latest changes from GitHub without changing your files.
merge origin/main brings your teammates' latest changes into your branch.

If your branch also exists on GitHub and a teammate might have pushed to it, also run:

bash
git pull
Step 4: if git says CONFLICT

This means you and a teammate changed the same lines, which is likely in CA2-analysis.md.

VS Code highlights the conflict in the file:
<<<<<<< HEAD
your version
=======
teammate's version
>>>>>>> origin/main
Above each conflict, VS Code shows buttons: Accept Current (yours), Accept Incoming (theirs) and Accept Both. Usually you want Accept Both, then delete anything duplicated.
Make sure no <<<<<<<, ======= or >>>>>>> lines are left.
Save the file, then run:
bash
git add .
git commit -m "Merge latest main"

If you're unsure which version to keep, send me the conflict text and I'll help.

Step 5: push
bash
git push

Alternative (if you don't want a WIP commit):

bash
git stash          # temporarily hide your changes
git pull           # get updates
git stash pop      # bring your changes back

This works too, but if there's a conflict it's a bit harder to handle. The commit method above is safer.

comment: I edited CA2-analysis.md without pulling the latest changes and had a git conflict. AI suggested committing my work, merging origin/main and resolving the conflict in VS Code. I followed these steps and checked that both my changes and my teammates' changes were kept.