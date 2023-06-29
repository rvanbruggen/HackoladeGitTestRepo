# HackoladeGitTestRepo
A small repo that would allow for quick demonstration of Hackolade GIT integration and collaboration.

The process will consist of a few parts.

## 1. We will create two separate CLONES of the repo on the local machine
See [this page](https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository). We do this because then we can simulate how two different users would have different clones, work on branches of these clones, and then merge them all together as needed.

## 2. Add a new Hackolade model to one of the clones of the repo
We are going to reverse engineer a MongoDB model file and store that in Clone1, the first clone of the repo. We do this in the `main` branch of the repo. When we then switch to the second clone, we will pull the latest changes and receive the new data model as part of the `main` branch as well.

## 3. In Clone1 - we add a new "Animals" feature
We will add an *animals* entity to the data model, including a foreign key relationship to the *movies* entity. We save that file in Clone1, in a separate "feature branch" - `feature-animals`. We push that branch to the remote.

## 4. In Clone2 - we add a new "Cars" feature
We will add an *cars* entity to the data model, including a foreign key relationship to the *movies* entity. We save that file in Clone2, in a separate "feature branch" - `feature-cars`. We push that branch to the remote.

## 5. In Clone1 - we submit the "Animals" feature for review.

## 6. In Clone2 - we submit the "Cars" feature for review

## 7. We will merge the "feature-animals" branch into "main" of Clone1

## 8. We will merge the "feature-cars" branch into "main" of Clone2

## 9. We will resolve the conflicts

## 10. We will delete the feature branches



##

