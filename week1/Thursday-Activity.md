# Assignment - Thursday, October 19th

1. Choose *one* group member to set up the group project for Week 1.
2. The group member selected should:
   1. Change the remote of the project from Tuesday's class to point to the Group*x*-Week1 project in the CS480 organization. Hint: `git remote set-url origin...`
   2. Push your code to the new remote. Hint: `git push -u origin --all`
   3. Switch to the main branch on your local machine and add a text file of some kind. Up to you what you do but put at least a couple of lines of text in there.
3. *After* one group member has done this, the other group members should re-clone this new repository.
4. Each group member should now *push* their branch that they created to the remote. 

At this point, you should have one copy of the Week1 project, with four or five branches - a `main` branch and each of your individual branches.

5. All group members: Switch to `main` and pull.
6. Switch to your own branch and `merge` the `main` branch.
7. On your branch you should now see all of the named `.py` files and the new text file created by your group "leader"
8. **Edit** the text file by adding a line at the very beginning of the file *and* making a unique change to one of the existing lines in the file.
9. Push your changes to your branch.

Ok, now it's time to get conflicted.

10. Switch to `main`. (Either one group member or all group members can do this - either way, you'll have some merge conflicts!)
11. Merge *all three or four* named branches in succession. You should start getting some merge conflicts along the way!
12. Decide as a group how to resolve the conflicts.
13. Resolve the conflicts and push the resolved merged commit to `main` on the remote.

