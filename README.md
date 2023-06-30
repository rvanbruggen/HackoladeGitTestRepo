# HackoladeGitTestRepo
A small repo that would allow for quick demonstration of Hackolade GIT integration and collaboration. We will explore different scenarios:
* *Scenario 1:* the scenario where we make a small change to a data model, that does not create a conflict, and automatically gets merged
* *Scenario 2:* the scenario where we make a small change to a data model, but that change DOES create a conflict that needs to be resolved
* *Scenario 3:* the scenario where we make large changes to a data model, in separate (feature) branches, and we merge these changes back together using pull requests

To do so, we have to follow a process that will consist of a few parts.

# Part 1: prepare

## 1. We will create two separate CLONES of the repo on the local machine
See [this page](https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository). We do this because then we can simulate how two different users would have different clones, work on branches of these clones, and then merge them all together as needed.

## 2. Add a new Hackolade model to one of the clones of the repo
We are going to reverse engineer a MongoDB model file and store that in Clone1, the first clone of the repo. We do this in the `main` branch of the repo. When we then switch to the second clone, we will pull the latest changes and receive the new data model as part of the `main` branch as well.

# Part 2: Scenario 1 - Small, non-conflicting change to the data model
We will make sure that both Clone1 and Clone2 are fully up-to-date and in sync with the Github remote.
Then, in Clone 1, we will add a new property `Score1` (Numeric) to the `Movies` collection. We will commit that change, but not push it yet.
Switching to Clone 2, we will add a new property `Score2` (Numeric) to the `Movies` collection. We will commit that change, and immediately push it. Therefore, the later change will have been synced to the remote, and the earlier change to the datamodel is still committed to Clone1 - but not yet synced to the remote.
We Switch to Clone 1, and push our commit to the remote. 
We will then find that we cannot immediately push: we have to pull first, to get the latest version of the model from the remote. After doing so, we can push the `Score2` change, and it will show us a conflict resolution screen. We can then solve the conflict. Once we resolve the conflict and push it into the remote, we will find that the model now has two additional `Score` attributes, 1 and 2.

Note that in this case, we chose to keep both Score1 and Score2 attributes in the data model. This would of course not always be true. In the conflict resolution screen, we can choose to keep only one of the `Score` attributes and allow only that one  to survive.


# Part 3: Scenario 2 - Small, conflicting change to the data model

# Part 4: Scenario 3 - Large, (feature) branch based change to the data model using pull request
## 1. In Clone1 - we add a new "Animals" feature
We will add an *animals* entity to the data model, including a foreign key relationship to the *movies* entity. We save that file in Clone1, in a separate "feature branch" - `feature-animals`. We push that branch to the remote.

## 2. In Clone2 - we add a new "Cars" feature
We will add an *cars* entity to the data model, including a foreign key relationship to the *movies* entity. We save that file in Clone2, in a separate "feature branch" - `feature-cars`. We push that branch to the remote.

## 3. In Clone1 - we submit the "Animals" feature for review.

## 4. In Clone2 - we submit the "Cars" feature for review

## 5. We will merge the "feature-animals" branch into "main" of Clone1

## 6. We will merge the "feature-cars" branch into "main" of Clone2

## 7. We will resolve the conflicts

## 8. We will delete the feature branches

# Part 5: Conclusion and wrap-up
