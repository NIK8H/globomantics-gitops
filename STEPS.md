### Objective 1: Access the Kubernetes Machine
Use the Lab Credentials and Instant Terminal to log into the Kubernetes machine, and access its CLI (Command Line Interface, or prompt). You will then perform some basic Kubernetes checks.
1.	Once the lab has spun up, and the Lab Credentials section is available on the right, copy its Public IP and store it locally for later use.
2.	Also copy the Password, and store it for later use.
3.	On the right, towards the top, click Instant Terminal. This will open a new browser tab showing the Instant Terminal command prompt.
4.	At the Instant Terminal $ command prompt, replace <<Public IP>> with the Public IP you copied earlier, and enter the following command:
5.	ssh cloud_user@<<Public IP>>
6.	When prompted to continue connecting, enter y.
7.	When prompted for the password, enter the Password you copied earlier.
You will be at the cloud_user@controlplane prompt.
If the prompt does not display @controlplane, but instead has @ip, the hostname isn't yet ready; log out by pressing Control+D, wait 30 seconds, and then ssh in again. Repeat until the prompt contains controlplane. This bit of the setup can take two to three minutes.
8.	Get the Kubernetes cluster information.
9.	kubectl cluster-info
The output shows the Kubernetes control plane is running. If you get other output or errors, the setup isn't yet complete. Periodically retry every 30 seconds until the output contains running. It can take about two to four minutes for the setup to complete.
10.	List the Kubernetes nodes.
11.	kubectl get nodes
The output shows the controlplane node with a status of Ready.
Great! You have successfully logged into AWS Console and verified the Kubernetes status.


### Objective 2: Set up a Core GitOps Environment with Argo CD
As a DevOps engineer at Globomantics, you must set up a GitOps environment using Argo CD. You will install Argo CD in your Kubernetes cluster, followed by Argo CD UI and CLI. You must also connect a Git repository as the source of truth for infrastructure configurations.
1.	Install Argo CD in your Kubernetes cluster.
1.	Create the argocd namespace.
2.	kubectl create namespace argocd
Expected Output: namespace/argocd created.
3.	Apply the Argo CD installation manifest.
4.	kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
Expected Output: Multiple resources created including serviceaccount/argocd-server and serviceaccount/argocd-application-controller.
5.	List the Argo CD pods.
6.	kubectl get pods -n argocd
The Argo CD pods will likely take a minute or two to complete their initialization. If pods are not in Running status, wait and retry the command periodically every 30 seconds until all pods show a Running status.
Expected Output: All pods show status Running (including argocd-server, argocd-application-controller, and the remainder of the pods).
7.	Expose the Argo CD API server through Argo CD UI in the background.
8.	kubectl port-forward svc/argocd-server -n argocd 8080:443 &
Note: The & at the end of the command runs the command in the background.
Expected Output: Forwarding from 127.0.0.1:8080 -> 443.
9.	Press Enter to continue.
10.	Verify the Argo CD UI is running.
11.	wget --no-check-certificate https://localhost:8080
Expected Output: 200 OK response indicating the Argo CD UI is accessible.
2.	Install and configure the Argo CD CLI.
1.	Download the CLI.
2.	curl -sSL -o argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
3.	chmod +x argocd
4.	sudo mv argocd /usr/local/bin/
Note: Enter the Password from the Lab Credentials section when prompted.
5.	Get the initial admin password.
6.	argocd admin initial-password -n argocd
Expected Output: A random password string
7.	Copy this password as you'll need it to login to Argo CD.
8.	Login to Argo CD, replacing <password> with the admin password you copied a bit earlier.
9.	argocd login localhost:8080 --username admin --password <password> --insecure
Expected Output: 'admin:login' logged in successfully.
Note: You can ignore the Unhandled Error, and note this is insecure, and more secure authentication via SSO would normally be recommended. However, this lab's focus is not on secure authentication, so using a password directly like this for this lab is fine.
10.	Verify the CLI is installed.
11.	argocd version
Expected Output: Shows Argo CD client and server version information.
Note: You can ignore the Unhandled Error here as well, and when this error crops up in some later argocd commands, you can ignore there as well.
3.	Create an empty GitHub repository, and set up a fine-grained access token.
1.	Open a new browser tab and navigate to github.com.
2.	Sign in to your GitHub account, or create a new account if you don't have one.
3.	Create a new repository.
1.	Click the + icon in the top-right corner and select New repository.
2.	Enter globomantics-gitops as the Repository name.
Do NOT initialize with README, .gitignore, or license.
3.	Click Create repository.
Expected Output: A new empty repository is created with the URL https://github.com/<username>/globomantics-gitops.git.
4.	Create a fine-grained personal access token.
1.	Click your profile picture in the top-right corner and select Settings.
2.	At the bottom of the left sidebar, click Developer settings <>.
3.	Click Personal access tokens then Fine-grained tokens.
4.	Click Generate new token.
5.	Enter a Token name of GitOps Lab Token.
6.	Under Repository access, select Only select repositories, then select the globomantics-gitops repository you just created.
7.	Under Permissions click + Add permissions, then select Contents.
8.	On the right of the new Contents row that's added, click the drop-down and select Read and write.
9.	Click Generate token, and on the pop-up click Generate token again.
10.	Copy your personal access token immediately and store it securely.
4.	Install the GitHub CLI, and connect your personal Git repository to Argo CD.
1.	Install GitHub CLI.
2.	sudo apt-get install gh
Enter the Password from the Lab Credentials section when prompted.
•	If you get a lock error, use sudo kill <PID> on the listed process (replace <PID> with that number), wait a few seconds, then try the apt-get command again. You may need to repeat this once or twice.
Expected Output: Installation progress and completion message.
3.	Store the GitHub token in the environment variable.
4.	export GH_TOKEN=<your-github-token>
Note: Replace <your-github-token> with your actual GitHub personal access token.
5.	Login to GitHub using your token.
6.	gh auth login
Press Enter to select the default GitHub.com from the list of providers.
Expected Output: The value of the GH_TOKEN environment variable is being used for authentication..
7.	Clone the original Globomantics repository.
8.	gh repo clone ps-interactive/lab_gitops-for-kubernetes-application-deployment
Expected Output: Repository cloned successfully.
9.	Navigate to the cloned repository.
10.	cd lab_gitops-for-kubernetes-application-deployment
11.	Change the remote origin to your new repository.
Replace <username> with your actual GitHub username.
git remote set-url origin https://github.com/<username>/globomantics-gitops.git
12.	Configure Git to store GitHub credentials.
13.	git config --global credential.helper store
Note: This will store the credentials in the ~/.git-credentials file when you enter them for the first time.
14.	Push the repository to your GitHub account.
15.	git push -u origin main --force
Enter your GitHub Username when prompted, and when prompted for you Password, enter the personal access token you created and copied earlier.
Expected Output: Branch 'main' set up to track 'origin/main'.
Note: The --force flag is used to overwrite the existing blank repository with the local repository.
5.	Using Argo CD CLI, add your repository to Argo CD.
1.	Add the repository to Argo CD.
Replace <username>, twice, with your GitHub username.
argocd repo add https://github.com/<username>/globomantics-gitops.git --username <username> --password $GH_TOKEN
Expected Output: Repository 'https://github.com/<username>/globomantics-gitops.git' added.
2.	Verify the repository is added.
3.	argocd repo list
Expected Output: Shows your repository in the list with status Successful.
Nice work! You've successfully set up a GitOps environment with Argo CD, and connected a personal Git repository to Argo CD.










### Objective 3: Deploy and Manage an Application on Kubernetes via GitOps

As a DevOps engineer at Globomantics, you must deploy the company's API service using Kubernetes Deployments. You'll create a deployment with multiple replicas and specific resource requirements to ensure reliable service operation. You'll also verify the deployment status and pod health. You'll also understand how to use Argo CD to deploy the application to the Kubernetes cluster.
1.	Define application manifests for the Globomantics application.
1.	Navigate to the manifests folder to store the Kubernetes manifest files.
2.	cd manifests
3.	Create a Kubernetes deployment manifest named deployment.yaml.
4.	vim deployment.yaml
5.	In vim, prepare to paste the yaml properly by pressing :, then entering set paste.
Then press i to enter INSERT mode.
6.	Paste the yaml content.
	```
    apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: globomantics
	  namespace: globomantics
	spec:
	  replicas: 2
	  selector:
	    matchLabels:
	      app: globomantics
	  template:
	    metadata:
	      labels:
	        app: globomantics
	    spec:
	      containers:
	      - name: globomantics
	        image: nginx:1.27.5
	        ports:
	        - containerPort: 80
    ```
Note: The manifest includes:
•	replicas: Number of Pods to create
•	selector: MatchLabels for Pod selection
•	namespace: Target namespace for the deployment
27.	Save and exit the file by pressing ESC, then typing :wq, and then Enter.
28.	Create a service manifest named service.yaml.
29.	vim service.yaml
30.	Paste in the yaml content as before, saving and exiting the file.

```
	apiVersion: v1
	kind: Service
	metadata:
	  name: globomantics
	  namespace: globomantics
	spec:
	  selector:
	    app: globomantics
	  ports:
	  - protocol: TCP
	    port: 80
	    targetPort: 80
	  type: ClusterIP
```
Note: The service manifest creates a ClusterIP service to expose the application internally.
44.	Set the email and name to be used for git operations.
45.	git config --global user.email "<your-email>"
46.	git config --global user.name "<your-name>"
Replace <your-name> and <your-email> with your name and email address.
47.	Commit your changes.
48.	git add . 
49.	git commit -m "Added deployment and service manifests"
50.	git push origin main
Expected Output: Shows commit hash and push confirmation.
2.	Synchronize application state with Argo CD.
1.	Create an Argo CD application named globomantics in manual sync mode.
2.	argocd app create globomantics \
3.	--repo <repository-url> \
4.	--path manifests \
5.	--dest-namespace default \
6.	--dest-server https://kubernetes.default.svc \
7.	--sync-policy=manual
Replace <repository-url> with your actual repository URL.
Expected Output: application 'globomantics' created.
8.	Synchronize your application.
9.	argocd app sync globomantics
Expected Output: Shows sync progress and completion with Message as successfully synced.
3.	Inspect and verify the deployed application.
1.	Check the application status.
2.	argocd app get globomantics
Expected Output: Shows application status as Healthy and Synced.
3.	View resources in Kubernetes.
4.	kubectl get all -n globomantics
Expected Output: Shows deployment, service, and pods in the globomantics namespace.
Good job! You've successfully deployed the application and synchronized application state with Argo CD.







### Objective 4: Practice Core Git Operations for GitOps Workflow
As the next step, you must execute Git operations to manage your application lifecycle by modifying the deployment manifest, committing the changes, and synchronizing the application with Argo CD. You will then rollback your application to the previous state by reverting the last commit. Finally, you must monitor the application logs to verify the rollback.
1.	Commit and push changes to the GitHub repository.
1.	Open the deployment.yaml file.
2.	vim deployment.yaml
3.	Change the replicas count to 4.
Navigate to the line replicas: 2, and put the cursor over the 2 (one way is to use the arrow keys). Then press r, and then 4.
4.	Save and exit the file by typing :wq, and then Enter.
5.	Commit your changes.
6.	git add . 
7.	git commit -m "Updated replica count"
8.	git push origin main
Expected Output: Shows commit hash and push confirmation.
2.	Synchronize your application.
3.	argocd app sync globomantics
Expected Output: Shows sync progress and completion with successfully synced.
4.	Verify the deployment of the Git changes.
1.	Check the deployment status.
2.	kubectl get deploy -n globomantics
Expected Output: Shows 4/4 replicas available.
3.	List the available Pods.
4.	kubectl get pods -n globomantics
Expected Output: Shows four pods with status Running.
5.	Perform rollback through Git.
1.	Revert the last commit.
2.	git revert HEAD -n
3.	git commit -m "Reverted last commit"
4.	git push origin main
Expected Output: Shows revert commit hash and push confirmation.
5.	Synchronize again via Argo CD.
6.	argocd app sync globomantics
Expected Output: Shows sync progress and completion.
7.	Check the deployment status.
8.	kubectl get deploy -n globomantics
Expected Output: Shows 2/2 replicas available.
6.	Monitor GitOps pipeline status.
1.	Check the logs of the application.
2.	argocd app logs globomantics
Expected Output: Shows application logs and sync history.
3.	Check the logs of the Deployment.
4.	kubectl logs deploy/globomantics -n globomantics
Expected Output: Shows container logs from the deployment.
Well done! You've committed and pushed changes to the GitHub repository, verified the automatic deployment of the Git changes, performed rollback through Git, and monitored the application logs.

