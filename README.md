# Tasks: expo project

Let us learn how to use git, docker, kubernetes and GitHub actions.

![Overview](Diagram.png)

## Level 0 - Git
1. Create a repository: in the project folder run `git init`, then `git branch -M main`.
2. Add a `.gitignore` for build output.
3. Make your first commit: `git add .` then `git commit -m "Initial commit"`. 
4. Create an empty repository on GitHub (no README), then connect it: `git remote add origin <your-repo-url>`. Check with `git remote -v`.
5. Push it: `git push -u origin main`. Refresh GitHub and confirm your files are there.
6. From now on, commit after each level: `git add .`, `git commit -m "Finish level N"`, `git push`.

## Level 1 - Docker: you need to have the expo.c file and the Dockerfile inside the folder to be able to build and run.
1. Build the image: `docker build -t expo:1.0 .`
2. Run it with `docker run --rm expo:1.0 2 10`. 
3. Run `docker run --rm expo:1.0 abc 2`. 
4. Check the image size: `docker images expo:1.0`. 
5. Build with a version: `docker build --build-arg VERSION=1.1 -t expo:1.1 .`, then run `docker inspect expo:1.1` and find the `version` label.
6. Run `bash test.sh` and make sure all tests pass.

## Level 2 - Kubernetes: you need to have the expo.c, Dockerfile, Job.yaml files to be able to run.
1. `minikube start`, then `minikube image load expo:1.0`. It copies a Docker image from your computer into Minikube’s own image storage, so Kubernetes can use it.
2. Run the good Job: `kubectl apply -f Job.yaml`, then `kubectl logs job/expo-job`
3. Run the bad Job: `kubectl apply -f Job-bad.yaml`. Use `kubectl get jobs`. Which one shows `Failed`? expo-bad-job.
4. Read why it failed: `kubectl logs job/expo-bad-job`
5. Change the args in `Job.yaml` to `["5", "3"]`, delete the old Job `kubectl delete -f Job.yaml`, apply again.
6. Run the CronJob: `kubectl apply -f CronJob.yaml`, wait 2 minutes, then `kubectl get jobs -A`.
6. Run the CronJob: `kubectl create job expo-manual --from=cronjob/expo-cron`, `kubectl logs job/expo-manual`, then `kubectl get jobs -A`.
7. Clean up: `kubectl delete -f Job.yaml -f Job-bad.yaml -f CronJob.yaml` or `kubectl delete job expo-manual` or `kubectl delete cronjob --all`. Now check `kubectl get jobs`.

## Level 3 - GitHub Actions: you need to have the expo.c, Dockerfile, Job.yaml, ci.yml and test.sh files to be able to run.
1. Put `ci.yml` in `.github/workflows/`. Run in your Mac terminal: `mkdir -p .github/workflows` then `cp ci.yml .github/workflows/`.
Run in your Windows terminal (PowerShell): `mkdir .github/workflows` then `Copy-Item ci.yml .github/workflows/`. Then you can commit it, and push to `main` (the repo from Level 0).
2. Open the Actions tab. Find the 4 jobs: lint, build-and-test, kubernetes-test, publish.
3. Download the `build-output` artifact and open `test-results.txt`.
4. Add a new test case to `test.sh` (for example `check "10 2" "10 ^ 2 = 100"`), commit, and push. 
5. Break it on purpose: create the branch with `git checkout -b broken`, change `pow(...)` to `base * exponent`, commit, push the branch, and watch which job fails.
6. Merge a fixed version back into `main` (`git checkout main`, `git merge broken` after fixing, or open a pull request) and check the package in the GitHub "Packages" section.

## Level 4 - Website: you need to have the expo.c, Dockerfile, Job.yaml, ci.yml, test-web.sh, index.html, ci-website.yml files to be able to run.
1. Put `ci.yml` in `.github/workflows/`. Run in your Mac terminal: `mkdir -p .github/workflows` then `cp ci.yml .github/workflows/`.
2. Put `ci-website.yml` in `.github/workflows/`. Run in your Mac terminal: `mkdir -p .github/workflows` then `cp ci-website.yml .github/workflows/`.
3. Make sure your image is `build docker build -t expo:1.0`. Then, start the server: `python3 server.py`, and open `http://localhost:8000`.
4. Calculate `2` and `10`. Does it match `docker run --rm expo:1.0 2 10`? Yes.
5. Type `abc` as the base. Which message do you see, and where does it come from? (Hint: `expo.c`)
6. Run the website tests: start `python3 server.py` in one terminal, then run `sh test-web.sh` in another. All tests should pass.
7. Put `ci-website.yml` in `.github/workflows/`, commit and push. Open the Actions tab and find the second pipeline, `CI Website`. Does `CI Website` pass?
8. Add a new check to `test-web.sh` (for example `check "3 ^ 4" "/calculate?base=3&exponent=4" "3 ^ 4 = 81"`), commit and push. Does `CI Website` still pass?

