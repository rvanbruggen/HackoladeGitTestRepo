# HackoladeGitTestRepo
This is a a small repo that would allow for quick demonstration of [the Hackolade Studio Workgroup Edition's](https://hackolade.com/editions.html) GIT integration and collaboration. 

## Introduction
In Hackolade, we leverage the concept of [Metadata-as-code](https://hackolade.com/metadata-as-code.html) the ensure that the data models that we create are always up to date, and in sync with the frequent releases of our agile development methdologies. In the Workgroup Edition of the Hackolade Studio, we therefore have implemented a very tight integration with GIT: distributed version congrol system that is extremely popular with agile development teams.

It is worth considering the specific nature of GIT for a moment. As you can read on the [main Git Website](https://git-scm.com/), it is 

    ...a free and open source *distributed* version control system designed to handle everything from small to very large projects with speed and efficiency.

    Git is easy to learn and has a tiny footprint with lightning fast performance. It outclasses SCM tools like Subversion, CVS, Perforce, and ClearCase with features like cheap local branching, convenient staging areas, and multiple workflows.
    
That all sounds really interesting, but the key parts here to us are that
* it is distributed
* it is fast
* it is powerful
* it accomodates agile workflows
That all makes it a much better fit for Hackolade's NOSQL and Agile data modeling requirements than centralised version control systems, which take a much more crude, all-or-nothing, shotgun-like approach to version management challenges.

The only downside that we see to GIT, is that the learning curve can be a bit steeper, and like with all distributed systems, they can become quite complex. We are therefore happy to provide a few reading materials for you to review:
* Our own [Hackolade documentation](https://hackolade.com/help/Concepts1.html) has a great page on the topic of GIT.
    * it also refers to [this great article that explains GIT branches using Lego](https://opensource.com/article/22/4/git-branches) :) 
* [What is version control: centralized vs. DVCS](https://www.atlassian.com/blog/software-teams/version-control-centralized-dvcs)
* [The differences between centralised and decentralised version control systems](https://www.tutorialspoint.com/difference-between-centralized-version-control-and-distributed-version-control)
* [Why move from centralised to decentralised version control](https://about.gitlab.com/blog/2020/11/19/move-to-distributed-vcs/)
* [Centralized vs Distributed Version Control: Which One Should We Choose?](https://www.geeksforgeeks.org/centralized-vs-distributed-version-control-which-one-should-we-choose/)

There are also some really great GIT courses online that could be useful: see [this article](https://medium.com/javarevisited/top-10-free-courses-to-learn-git-and-github-best-of-lot-967aa314ea) for some great links.

## Using git in Hackolade Studio Workgroup Edition
In the repo, we will explore different and realistic scenarios of data modeling collaboration using GIT. On this page, we have developed a number of (hopefully) interesting scenarios:
* *Scenario 1:* the scenario where we make a small change to a data model, that does not create a conflict, and automatically gets merged
* *Scenario 2:* the scenario where we make a small change to a data model, but that change DOES NOT require manual intervention to resolve the conflict outside of the normal GIT workflow
* *Scenario 3:* the scenario where we make a small change to a data model that requires a manual intervention in the GIT workflow to resolve it
* *Scenario 4:* the scenario where we make large changes to a data model, in separate (feature) branches, and we merge these changes back together using pull requests. We have created two separate versions of this scenario, as _pull requests_ are a very important way to use GIT effectively, at scale.

So let's get started, and start our preparation.

## Part 1: prepare

### 1. We will create two separate CLONES of the repo on the local machine
See [this page](https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository). We do this because then we can simulate how two different users would have different clones, work on branches of these clones, and then merge them all together as needed.

### 2. Add a new Hackolade model to one of the clones of the repo
We are going to reverse engineer a MongoDB model file and store that in Clone1, the first clone of the repo. We do this in the `main` branch of the repo. When we then switch to the second clone, we will pull the latest changes and receive the new data model as part of the `main` branch as well. In the examples below, we will be using the MongoDB Atlas [Mflix Dataset](https://www.mongodb.com/docs/atlas/sample-data/sample-mflix/) that is loaded by default in an Atlas free instance. This data model contains 5 collections:
* movies: Contains movie information, including release year, director, and reviews.
* comments: Contains comments associated with specific movies.
* theaters: Contains locations of movie theaters.
* sessions: Metadata field. Contains users' _JSON Web Tokens_.
* users: Contains user information.

In a normal situation, different users will be working on different clones of the repo, and as they do, they can just commit their changes and it will not result in any conflict. If, however, users start to work on their local clones at the same time, and make changes to these clones at the same time, then the chance of conflicts between these changes arises. Hackolade has taken great care to structure the file format of the data model in such a way that many of these simultaneous edits can be automatically resolved. We will therefore demonstrate both the case where a parallel edit to a data model gets automatically, and manually resolved.


## Part 2: Different scenarios for conflict resolution

### Scenario 1 - Small, conflicting change to the data model that is cosmetic and is auto-resolved
We will make sure that both Clone1 and Clone2 are fully up-to-date and in sync with the GIT remote. Then we proceed as follows:
* in the ERD of Clone1, we move the `comments` entity to the right, and keep the `sessions` entity to the left of the diagram. We commit this change, but don't push it to the remote yet.
* in the ERD of Clone2, we move the `comments` entity and the `sessions` entity to the right of the diagram. We commit this change, and push it to the remote.
* in Clone1, we have to pull first, and then try to push our earlier change to the remote. A conflict will occur, but it will be automatically resolved after a few seconds. In our Hackolade client, we will see a new local commit that we need to push to the remote. It will be marked with `Hackolade auto-resolve commit`. 
* In Clone 2, we will then need to pull the two new commits (the auto resolved and the original change of the position of the Collection in the ERD) before proceeding.

You can try this a few times. Just moving the Entity boxes around in the ERD potentially leads to conflicts between clones, but these conflicts are purely cosmetic and are automatically resolved.

### Scenario 2 - Small, non-conflicting / non-cosmetic change to the data model with git-based resolution

We will make sure that both Clone1 and Clone2 are fully up-to-date and in sync with the Github remote.
Then, we proceed as follows:
* in Clone 1, we will add a new property `Score1` (Numeric) to the `Movies` collection. We will commit that change, but not push it yet.
* Switching to Clone 2, we will add a new property `Score2` (Numeric) to the `Movies` collection. We will commit that change, and immediately push it. Therefore, the later change will have been synced to the remote, and the earlier change to the datamodel is still committed to Clone1 - but not yet synced to the remote.
* We Switch to Clone 1, and push our commit to the remote. We will then find that we cannot immediately push: we have to pull first, to get the latest version of the model from the remote (which already includes the `Score2` property that we added to Clone2, and committed and pushed immediately in the previous step). 
* After doing so, we can push the `Score2` change, and the application wil tell us that we need to resolve a conflict first.
* Pushing the button will show us a conflict resolution screen. We can then solve the conflict. Once we resolve the conflict and push it into the remote, we will find that the model now has two additional `Score` attributes, `Score1` and `Score2`.

Note that in this case, we chose to keep both Score1 and Score2 attributes in the data model. This would of course not always be true. In the conflict resolution screen, we can choose to keep only one of the `Score` attributes and allow only that one  to survive.

### Scenario 3 - Small, conflicting change to the data model with git-based resolution

We will make sure that both Clone1 and Clone2 are fully up-to-date and in sync with the Github remote.
Then, we proceed as follows:
* In Clone 1, we will add a new property `Score` (Numeric) to the `Movies` collection. We commit and push that change to the repo
* In Clone 2, we will pull from the remote, ensuring that both clones now have the latest copy of the remote. 
* In Clone 2, we will update the description of the `Score` property to say `This is the description of the score from Clone2.`. We will commit that change locally but NOT push that to the remote.
* Switching to Clone 1, we will also update the description of the `Score` property to say `This is the description of the score from Clone1.`. We will commit that change locally and also push it to the remote.
* Switching to Clone2, we will push the change that we had committed locally to the remote. Howeverm before we can do that, we will first need to pull - as Clone2 is now _behind_ on the previous commit+push from clone1. 
* When we do that pull, we will see that there is a conflict that needs to be resolved first: both clone1 and clone2 have been editing the same property description, and therefore, the conflict resolution screen will help us decide which of the two versions will need to prevail.
Once that's done, the correct version will be on the remote, and we will be able to pull that to every clone as appropriate.


### Scenario 4 - Large, (feature) branch based change to the data model using pull request

The idea is that we will have 
* two clones of the same repo, Clone1 and Clone2
* in both Clones, work on the repo will be done in parallel. This will be done in separate branches, which will then be merged into the main branch after specific changes have been performed.  In the scenario below, we will talk about two parallel features being added to the data model - one related to "Animals" (in movies), and one related to "Cars" (in movies).

#### 1. In Clone1 - we add a new "Animals" feature
We will add an *animals* entity to the data model (including an `_id` (OId), a `name` (str), and `movie_id` (OId) properties), including a foreign key relationship to the *movies* entity. We save that file in Clone1, in a separate "feature branch" - `feature-animals`. We push that branch to the remote.

#### 2. In Clone2 - we add a new "Cars" feature
We will add an *cars* entity to the data model (including an `_id` (OId), a `make` (str), a `model` (str),and `movie_id` (OId) properties), including a foreign key relationship to the *movies* entity. We save that file in Clone2, in a separate "feature branch" - `feature-cars`. We push that branch to the remote.

#### 3. In Clone1 - we submit the "Animals" feature for review.
In the "Submit for review" part of the Hackolade Studio's GIT collaboration environment, we create a new request to merge the `feature-animals` branch into the `main` branch of the Clone1 repo. We do that by opening a *pull request*. We can optionally assign the review of the request to another user if desired.

Once we have created the request, we switch to the Clone2 environment.
#### 4. In Clone2 - we submit the "Cars" feature for review
In the "Submit for review" part of the Hackolade Studio's GIT collaboration environment, we create a new request to merge the `feature-cars` branch into the `main` branch of the Clone2 repo. We do that by opening a *pull request*. We can optionally assign the review of the request to another user if desired.

#### 5. We will merge the "feature-animals" branch into "main" of Clone1
From the `Check pull requests` part of the Hackolade Studio's GIT collaboration environment, we open the first, Clone1-based pull request to add the Animals-feature. We can review the changes, and see that a new collection (the `animals` collection) was added, and then we can *Merge* this into the `main` branch.
#### 6. We will merge the "feature-cars" branch into "main" of Clone2
From the `Check pull requests` part of the Hackolade Studio's GIT collaboration environment, we open the second, Clone2-based pull request to add the Cars-feature. However, given the fact that the Animals-feature was just merged into the `main` branch, the client will say that we need to update the branch first as the `feature-cars` branch is out-of-date with the branch main. 

When we update the branch, the Hackolade Studio user interface will request that we *Solve the conflicts* in the file first.
#### 7. We will resolve the conflicts
In the `Solve Conflicts` screen, the Hackolade Studio will show us
* the recently merged model that already has the Animals feature in it,
* the proposed merger of the Cars feature into the model
* the proposed merged model where both the Animals and the Cars features, both the collections and the relationships, have been added to the model.
By clicking the `Solve` button, the conflict is resolved and ready to be merged. We do so by clicking the `Merge` button.

Now, we can switch to the `Main` branch, pull all the changes and see the merged model with both the Cars and the Animals features in it.
#### 8. We will delete the feature branches
Now that both features have been added to the `Main` branch, we can delete the `feature-cars` and `feature-animals` branches, as they are not needed anymore. To do so, we will need to confirm by typing the name of the branch, and chosing to delete the remote branch as well.


###  Scenario 4bis - Large, (feature) branch based change to the data model using pull request
Based on this
![](https://hackolade.com/img/Versioning%20-%20model%20lifecycle.png)
This scenario is also described [in our documentation](https://hackolade.com/help/Modelversioning.html). 

In this scenario, we have two branches as well. Like in the documentation, we talk about a "minor fixes" and a "new features" branch.
1. The first branch covers minor fixes to the main branch, and is frequently merged into minor releases to the model.
2. the second branch covers major feature additions to the main branch, and is reqularly, but less frequently merged into major releases to the model.

#### 1. Minor fixes branch
We first add a change to Clone1: Add description of `Movies` Collection.
We commit and push this to branch `minor-fixes`.

#### 2. New "Animals" features branch
Then we make a change to Clone2: a new `animals` collection.
This is a new feature that could become quite a big change to the model. For now, we add only the `_id` (OId) attribute to the collection.
We commit and push this to a `new-features` branch.

#### 3. Adding small changes to minor fixes
In Clone1, we add a second line to the description of the `Movies` collection.
We commit and push this to branch `minor-fixes`.

#### 4. Develop the "Animals" feature
In Clone2, we add a `name` (str) property to the `animals` collection.
We commit and push this change to the `new-features` branch.

#### 5. Merge the minor fixes into the main branch - version 1.1
In Clone1, open a pull request.
Merge the pull request into main.
Main now has version 1.1.

#### 6. Enhance the "Animals" feature
In Clone2, add a `movie_id` (OId) property to the `Animals` collection.
We commit and push this to the `new-features` branch.
In Clone2, we make another change and add a FK relationship from `animals.movie_id` to `movies._id`. The parent cardinality is `1`. The child cardinality is `0..n`.
W commit and push this to the `new-features` branch.

#### 7. Adding another small change to minor fixes
In Clone1, we add a third line to the description of the `Movies` collection.
We commit and push this to branch `minor-fixes`.

#### 8. Merge the minor fixes into the main branch - version 1.2
In Clone1, we open a pull request to merge `minor-fixes` into the `main` branch.
We merge the pull request into the `main` branch.

#### 9. Merge the `animals` new feature into the mail branch - version 2.0
In Clone2, we open a pull request to add the `new-features` developed into the `main` branch, creating version 2.0
We update Clone2 to make sure that it has the latest version of the `main` branch.
We know that `main` has evolved in the mean while, and is at version 1.2 - no longer at 1.0 where `new-features` was branched from. We therefore need to solve the conflicts. Once we do, we can merge the pull request into the `main` as well, creating version 2.0 in that branch.
Then we pull the latest version from `main`, and this allows us to see the merger of all of the changes, both the minor ones and the major new feature development. It will all be one big new version of the model, in the `main` branch.

## Part 3: Conclusion and wrap-up
In this repo, we have tried to illustrate the different scenarios under which we can see the power of *metadata-as-code* at work. Please try this yourself, using the model that you can find in this repo - it is there to be forked.


