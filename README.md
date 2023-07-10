# HackoladeGitTestRepo
A small repo that would allow for quick demonstration of [the Hackolade Studio Workgroup Edition's](https://hackolade.com/editions.html) GIT integration and collaboration. We will explore different and realistic scenarios of data modeling collaboration on this page:
* *Scenario 1:* the scenario where we make a small change to a data model, that does not create a conflict, and automatically gets merged
* *Scenario 2:* the scenario where we make a small change to a data model, but that change DOES NOT require manual intervention to resolve the conflict outside of the normal GIT workflow
* *Scenario 3:* the scenario where we make a small change to a data model that requires a manual intervention in the GIT workflow to resolve it
* *Scenario 4:* the scenario where we make large changes to a data model, in separate (feature) branches, and we merge these changes back together using pull requests

To do so, we have to follow a process that will consist of a few parts.

# Part 1: prepare

## 1. We will create two separate CLONES of the repo on the local machine
See [this page](https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository). We do this because then we can simulate how two different users would have different clones, work on branches of these clones, and then merge them all together as needed.

## 2. Add a new Hackolade model to one of the clones of the repo
We are going to reverse engineer a MongoDB model file and store that in Clone1, the first clone of the repo. We do this in the `main` branch of the repo. When we then switch to the second clone, we will pull the latest changes and receive the new data model as part of the `main` branch as well. In the examples below, we will be using the MongoDB Atlas [Mflix Dataset](https://www.mongodb.com/docs/atlas/sample-data/sample-mflix/) that is loaded by default in an Atlas free instance. This data model contains 5 collections:
* movies: Contains movie information, including release year, director, and reviews.
* comments: Contains comments associated with specific movies.
* theaters: Contains locations of movie theaters.
* sessions: Metadata field. Contains users' _JSON Web Tokens_.
* users: Contains user information.

In a normal situation, different users will be working on different clones of the repo, and as they do, they can just commit their changes and it will not result in any conflict. If, however, users start to work on their local clones at the same time, and make changes to these clones at the same time, then the chance of conflicts between these changes arises. Hackolade has taken great care to structure the file format of the data model in such a way that many of these simultaneous edits can be automatically resolved. We will therefore demonstrate both the case where a parallel edit to a data model gets automatically, and manually resolved.


# Part 2: Different scenarios for conflict resolution

## Scenario 1 - Small, conflicting change to the data model that is cosmetic and is auto-resolved
We will make sure that both Clone1 and Clone2 are fully up-to-date and in sync with the GIT remote. Then we proceed as follows:
* in the ERD of Clone1, we move the `comments` entity to the right, and keep the `sessions` entity to the left of the diagram. We commit this change, but don't push it to the remote yet.
* in the ERD of Clone2, we move the `comments` entity and the `sessions` entity to the right of the diagram. We commit this change, and push it to the remote.
* in Clone1, we have to pull first, and then try to push our earlier change to the remote. A conflict will occur, but it will be automatically resolved after a few seconds. In our Hackolade client, we will see a new local commit that we need to push to the remote. It will be marked with `Hackolade auto-resolve commit`. 
* In Clone 2, we will then need to pull the two new commits (the auto resolved and the original change of the position of the Collection in the ERD) before proceeding.

You can try this a few times. Just moving the Entity boxes around in the ERD potentially leads to conflicts between clones, but these conflicts are purely cosmetic and are automatically resolved.

## Scenario 2 - Small, non-conflicting / non-cosmetic change to the data model with git-based resolution

We will make sure that both Clone1 and Clone2 are fully up-to-date and in sync with the Github remote.
Then, we proceed as follows:
* in Clone 1, we will add a new property `Score1` (Numeric) to the `Movies` collection. We will commit that change, but not push it yet.
* Switching to Clone 2, we will add a new property `Score2` (Numeric) to the `Movies` collection. We will commit that change, and immediately push it. Therefore, the later change will have been synced to the remote, and the earlier change to the datamodel is still committed to Clone1 - but not yet synced to the remote.
* We Switch to Clone 1, and push our commit to the remote. We will then find that we cannot immediately push: we have to pull first, to get the latest version of the model from the remote (which already includes the `Score2` property that we added to Clone2, and committed and pushed immediately in the previous step). 
* After doing so, we can push the `Score2` change, and the application wil tell us that we need to resolve a conflict first.
* Pushing the button will show us a conflict resolution screen. We can then solve the conflict. Once we resolve the conflict and push it into the remote, we will find that the model now has two additional `Score` attributes, `Score1` and `Score2`.

Note that in this case, we chose to keep both Score1 and Score2 attributes in the data model. This would of course not always be true. In the conflict resolution screen, we can choose to keep only one of the `Score` attributes and allow only that one  to survive.

## Scenario 3 - Small, conflicting change to the data model with git-based resolution

We will make sure that both Clone1 and Clone2 are fully up-to-date and in sync with the Github remote.
Then, we proceed as follows:
* In Clone 1, we will add a new property `Score` (Numeric) to the `Movies` collection. We commit and push that change to the repo
* In Clone 2, we will pull from the remote, ensuring that both clones now have the latest copy of the remote. 
* In Clone 2, we will update the description of the `Score` property to say `This is the description of the score from Clone2.`. We will commit that change locally but NOT push that to the remote.
* Switching to Clone 1, we will also update the description of the `Score` property to say `This is the description of the score from Clone1.`. We will commit that change locally and also push it to the remote.
* Switching to Clone2, we will push the change that we had committed locally to the remote. Howeverm before we can do that, we will first need to pull - as Clone2 is now _behind_ on the previous commit+push from clone1. 
* When we do that pull, we will see that there is a conflict that needs to be resolved first: both clone1 and clone2 have been editing the same property description, and therefore, the conflict resolution screen will help us decide which of the two versions will need to prevail.
Once that's done, the correct version will be on the remote, and we will be able to pull that to every clone as appropriate.


## Scenario 4 - Large, (feature) branch based change to the data model using pull request
### 1. In Clone1 - we add a new "Animals" feature
We will add an *animals* entity to the data model (including an `_id` (OId), a `name` (str), and `movie_id` (OId) properties), including a foreign key relationship to the *movies* entity. We save that file in Clone1, in a separate "feature branch" - `feature-animals`. We push that branch to the remote.

### 2. In Clone2 - we add a new "Cars" feature
We will add an *cars* entity to the data model (including an `_id` (OId), a `make` (str), a `model` (str),and `movie_id` (OId) properties), including a foreign key relationship to the *movies* entity. We save that file in Clone2, in a separate "feature branch" - `feature-cars`. We push that branch to the remote.

### 3. In Clone1 - we submit the "Animals" feature for review.

### 4. In Clone2 - we submit the "Cars" feature for review

### 5. We will merge the "feature-animals" branch into "main" of Clone1

### 6. We will merge the "feature-cars" branch into "main" of Clone2

### 7. We will resolve the conflicts

### 8. We will delete the feature branches

# Part 3: Conclusion and wrap-up




# Temporary notes:

* leverage the scenarios described [in our documentation](https://hackolade.com/help/Modelversioning.html)
![](https://hackolade.com/img/Versioning%20-%20model%20lifecycle.png)

Branching scenarios:
* people collaborating in the same branch
    No different from working in main - main is "just a branch"
    Checkout a branch: different term
        does not mean locking
        means: this branch is active on my machine. it means "activating" it.
            you can only have one branch checked out / active at the same time
            checking out another branch, deactivates the first branch
        if I make a change, it impacts that active machine

You can have branches of branches
You have to decide where to merge into


* people collaborating in separate parallel branches
    * then we will have to work with Pull requests
    * Pull request = review request
        * gitlab calls it a change request
    * You then publish your changes so that other people can review it, but not yet affect the production version of the repo

* Workflow does not exist in GIT client. It exists in the GIT platform.
* Merge and Squash+Merge
    * Merge: each commit separately merged
    * Squash and Merge: puts everyt
