# CKAD Exam experience and tips
Hey Everyone, Just got my CKAD results and I passed with 85 marks. Sharing my experience here so that it could be helpful to others.

## Preparation
Resources:

Kodekloud (CKAD)

Killer.sh

I used Kodekloud to learn basic concepts and test them in their virtual simulators. One week before the exam, I used Killer.sh mock tests to gauge my depth. I like the killer.sh questions much better than actual exam.

### Exam Check in
With 30 minutes to exam, I logged in and downloaded the PSI browser. Downloading and setting up the process was good. But the check in process, not so much.

I have a Logitech Webcam used mainly for meetings. Somehow my ID was getting blurry when captured from webcam on their end. It was legible from my end. So I asked the proctor on what could be done. He replied contact the Support and closed the session. The browser got closed along with it. But the support is reachable only via chat or International numbers. Living in India , somehow on that day, the numbers were unreachable. The next 2 hours were a cat and mouse game where proctor would close the chat where I was chatting with the support and by the time I restart the browser, the support person would have left. I tried changing my device as well but same issue.

Finally, one proctor had some pity and waited for few minutes for support to get my details. The support then called me on my cellphone and we could proceed from there. 10 AM exam, started in 12:35 PM.

# Exam
The exam was on a Remote Desktop similar to Killer.sh simulator. The exam was easier than killer.sh. The questions were of following types

Fixing existing manifest files with updates.

Fixing non working resources (svc, ingress etc)

Creating resources from scratch.

Apart from occasional multiple keystrokes, the exam went smooth (My GPU was running at full speed though)

Document links to relevant topic was attached to the questions, this was a big help. Further, kubectl autocompletion was enabled. Did not expect that but helped me complete the exam faster.

## Tips
1. Learn Kubectl. Use the following to get templates built ready in seconds, instead of writing from scratch.

Use the args , ``--dry-run=client -o yaml`` to get the manifest and redirect the output to a file. This trick saved me some 10-20 minutes in exam.

For eg, ``kubectl -n namespace create deploy app --image=nginx:stable --replicas=2 --dry-run=client -o yaml > app-deploy.yaml``

2. If you get stuck in any question, mark it for review and move to next. There were far easier questions in the end.

3. Bookmark/Open https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands on the exam browser as soon as you log in. This would be helpful if you forget any kubectl commands

4. The exam uses multiple kubernetes context. Be sure to run the commands on the correct context.

5. While modifying any file, make a copy beforehand

6. Learn docker commands (build, tag, save, inspect) as well.

Overall Experience: 8/10