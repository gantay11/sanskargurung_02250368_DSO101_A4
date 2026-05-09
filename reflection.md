
## 1. Introduction
Assignment IV required me to build a simple static website, push it to GitHub, set up a GitHub Actions workflow, and deploy it automatically to Render.com. Compared to Assignment III which involved Docker and DockerHub, this assignment focused on a simpler but equally important deployment pattern — deploying a static site directly from a GitHub repository. This reflection documents what I learned, the mistakes I made, and how I overcame them.

## 2. What I Learned

### Deploying Static Sites
I learned the difference between a Static Site and a Web Service on Render.com. A Static Site is for plain HTML, CSS, and JavaScript files that don't need a server to run. There is no Docker, no Node.js, and no build command required — Render simply serves the files directly. This is the simplest and fastest way to get a website live on the internet.

### GitHub Actions for Static Sites
In Assignment III, the GitHub Actions workflow had many steps — installing Node.js, running tests, building Docker images, and pushing to DockerHub. For a static site, the workflow is much simpler. It only needs to check out the code and trigger a Render redeploy via a webhook URL. This taught me that CI/CD pipelines can be as simple or as complex as the project requires.

### Render Deploy Hooks
I learned how Render's deploy hook system works. When a new push happens, GitHub Actions sends a POST request to a special Render URL, which tells Render to pull the latest code and redeploy the site. This is the same mechanism used in Assignment III, but applied to a static site instead of a Docker container.

### Secrets Security
One of the most important lessons from this assignment was about secrets management. I accidentally pasted the Render deploy hook URL into my README.md file, which would have made it publicly visible on GitHub. I caught this mistake quickly, removed it from the README, and regenerated the deploy hook on Render to invalidate the exposed URL. This reinforced how critical it is to keep secrets out of source code.

### HTML and CSS Portfolio
Building the portfolio website itself was a good exercise in HTML and CSS. I created a multi-section page with a header, navigation bar, About, Skills, Projects, and Contact sections. Seeing it live on the internet at a real URL was very satisfying and motivating.

## 3. Challenges Faced and How I Overcame Them

### Challenge 1: Accidentally exposing the deploy hook URL
While updating my README.md, I accidentally pasted the Render deploy hook URL (which is a secret) instead of the live website URL. This is a serious security mistake because the deploy hook URL gives anyone the ability to trigger a redeployment of my app.

**How I fixed it**: I immediately corrected the README.md with the proper live URL, pushed the fix, and went to Render Settings to regenerate the deploy hook. Regenerating the hook creates a new URL and permanently invalidates the old one, so even if someone had seen it, it would no longer work. I then updated the GitHub secret with the new URL.

**What I learned**: Always double-check what you are pasting into public files. The deploy hook URL and the live site URL look very different — one starts with `https://api.render.com/deploy/...` and the other is a normal website URL. Never copy-paste sensitive values into README or any source file.

### Challenge 2: Workflow failing after regenerating the deploy hook
After regenerating the Render deploy hook, my GitHub Actions workflow kept failing because the `RENDER_DEPLOY_HOOK_URL` secret still contained the old, now-invalid URL. The workflow was successfully running but the curl request was being rejected by Render.

**How I fixed it**: I went to GitHub → Settings → Secrets → Actions → clicked the edit (pencil) icon next to `RENDER_DEPLOY_HOOK_URL` → pasted the new deploy hook URL → saved. After triggering the workflow again, it turned green.

**What I learned**: When you regenerate or rotate a secret on any platform, you must immediately update all places where that secret is stored — including GitHub Secrets, environment files, and any other integrations.

### Challenge 3: Choosing the right Render service type
Initially I was unsure whether to use "Web Service" or "Static Site" on Render. In Assignment III, I used Web Service because the app needed Node.js to run. For this assignment, the website is just HTML and CSS files — there is no server needed.

**How I fixed it**: I selected Static Site, set the Publish Directory to `.` (meaning serve files from the root folder), and left the Build Command empty. Render deployed the site in under one minute.

**What I learned**: Understanding the type of application you are deploying is essential for choosing the right deployment method. Static sites are simpler, faster, and free to host on Render without any configuration overhead.

## 5. What I Would Do Differently

- I would be more careful when copy-pasting URLs, especially into public files like README.md.
- I would set up the GitHub secret before writing the README to avoid any confusion between the deploy hook URL and the live site URL.
- I would add a custom domain to the Render deployment to make the URL look more professional.

## 6. Conclusion

Assignment IV gave me hands-on experience with the simplest form of CI/CD — automatically deploying a static website to the cloud on every code push. Despite being simpler than Assignment III, it reinforced critical concepts like secrets management, webhook-based deployments, and the importance of choosing the right deployment method for the right type of application. The mistake I made with the exposed secret was a valuable real-world lesson that I will not forget. I now feel confident deploying both static sites and containerized applications using modern CI/CD pipelines.