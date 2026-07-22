# Assignment 5 — Bash Script Automation Drill (OPS Checklist)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will practice Bash scripting by building a series of small automation scripts covering environment setup, variables, arrays, loops, file conditionals, if-else logic, and functions. These scripts form the foundation of real-world Linux automation used in DevOps, cloud, and production support environments.

---

# Task 1 — Bash Environment & Workspace Setup

## Goal

Verify that Bash is available on your system and create a clean workspace for this assignment.

### Evidence

#### Screenshot 1 — Output of `echo $SHELL` and `bash --version`

![Screenshot 1 — Output of `echo $SHELL` and `bash --version`](screenshots/Ass5-01.png)
---

#### Screenshot 2 — Output of `pwd` and `ls -lah` showing the scripts directory

![Output of `pwd` and `ls -lah`](screenshots/Ass5-02.png)
---

### Notes

Answer the following in your own words:

**1. What is Bash?**

Bash is a command-line interpreter that lets me interact with the Linux operating system by typing commands instead of using a graphical interface. 

---

**2. What is the difference between shell and Bash?**

A shell is the general program that accepts and runs commands from the user. Bash (Bourne Again Shell) is one specific type of shell. In other words, Bash is a shell, but not every shell is Bash. There are other shells like Zsh, Fish, and Ksh, each with its own features.
---

**3. Why is it important to confirm the Bash version before writing scripts?**

Checking the Bash version helps ensure that the features and syntax used in a script are supported on the system.
---

# Task 2 — Your First Bash Script

## Goal

Create your first Bash script, make it executable, and run it from the terminal.

### Evidence

#### Screenshot 1 — Content of `first-script.sh`

Add your screenshot here.

---

#### Screenshot 2 — Output of `./first-script.sh`

Add your screenshot here.

---

#### Screenshot 3 — Output of `ls -l first-script.sh` showing executable permission

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. What is the purpose of `#!/bin/bash`?**

Add your answer here.

---

**2. Why do we use `chmod +x` before running a script?**

Add your answer here.

---

**3. What is the difference between running a script using `./script.sh` and `bash script.sh`?**

Add your answer here.

---

# Task 3 — Variables: User Information Script

## Goal

Use variables to store and display user-related information.

### Evidence

#### Screenshot 1 — Content of `user-info.sh`

![Screenshot 1 — Content of `user-info.sh`](screenshots/Ass5-03.png)
---

#### Screenshot 2 — Output of `./user-info.sh`

![Screenshot 2 — Output of `./user-info.sh`](screenshots/Ass5-04.png)
---

### Notes

Answer the following in your own words:

**1. What is a variable in Bash?**

A variable is a named container used to store information such as text or numbers. It allows a script to reuse the same value whenever needed without typing it repeatedly.
---

**2. Why should we avoid spaces around the `=` sign when creating variables?**

Bash treats spaces as separators between commands and arguments. If spaces are added around the =, Bash will not recognize it as a variable assignment and the script will produce an error.
---

**3. How do you access the value stored inside a Bash variable?**

You access a variable's value by placing a dollar sign ($) before its name. For example, $full_name displays the value stored in the variable.
---

# Task 4 — Arrays & Loops: Tools Checklist Script

## Goal

Use arrays and loops to print a checklist of tools used in Bash scripting.

### Evidence

#### Screenshot 1 — Content of `tools-checklist.sh`

![Screenshot 1 — Content of `tools-checklist.sh`](screenshots/Ass5-05.png)
---

#### Screenshot 2 — Output of `./tools-checklist.sh`

![Screenshot 2 — Output of `./tools-checklist.sh`](screenshots/Ass5-06.png)
---

### Notes

Answer the following in your own words:

**1. What is an array in Bash?**

An array is a collection of multiple values stored under one variable name. Each value can be accessed individually using its position in the array.
---

**2. Why are arrays useful in scripts?**

Arrays make it easier to manage groups of related data. Instead of creating many separate variables, you can store everything together and process the values using loops.
---

**3. What does `"${tools[@]}"` mean?**

It represents all the elements stored in the tools array. It allows the script to access every item one after another.
---

**4. What is the purpose of the `for` loop in this script?**

The for loop goes through each item in the array and performs the same action on every element, avoiding the need to repeat the same code.
---

# Task 5 — Loops: Number Counter Script

## Goal

Use loops to repeat a task multiple times.

### Evidence

#### Screenshot 1 — Content of `counter.sh`

![Screenshot 1 — Content of `counter.sh`](screenshots/Ass5-07.png)
---

#### Screenshot 2 — Output of `./counter.sh`

![Screenshot 2 — Output of `./counter.sh`](screenshots/Ass5-08.png)
---

### Notes

Answer the following in your own words:

**1. What is a loop?**

A loop is a programming structure that repeats a block of commands until a specified condition has been met.
---

**2. Why do we use loops in Bash scripting?**

Loops help automate repetitive tasks, making scripts shorter, easier to maintain, and more efficient.
---

**3. How many times did the loop run in your script?**

The loop ran 5 times, printing the numbers from 1 to 5.
---

**4. What would you change if you wanted the loop to run 10 times?**

I would change the loop range from 1..5 to 1..10 so it repeats ten times instead of five.
---

# Task 6 — Files & Conditionals: File Validation Script

## Goal

Use file checks and conditionals to verify whether files and directories exist.

### Evidence

#### Screenshot 1 — Output of `ls -lah ../test-folder`

![Screenshot 1 — Output of `ls -lah ../test-folder`](screenshots/Ass5-09.png)
---

#### Screenshot 2 — Content of `file-check.sh`

![Screenshot 2 — Content of `file-check.sh`](screenshots/Ass5-10.png)
---

#### Screenshot 3 — Output of `./file-check.sh`

![Screenshot 3 — Output of `./file-check.sh`](screenshots/Ass5-11.png)
---

### Notes

Answer the following in your own words:

**1. What does `-d` check in Bash?**

The -d option checks whether the specified path exists and is a directory.
---

**2. What does `-f` check in Bash?**

The -f option checks whether the specified path exists and is a regular file.---

**3. Why should file and directory paths be stored in variables?**

Storing paths in variables makes scripts easier to update and reduces errors because the path only needs to be changed in one place.
---

**4. What happens if the file does not exist?**

The file check returns false, allowing the script to execute the alternative action, such as displaying an error message or skipping the operation.
---

# Task 7 — Conditionals: Pass or Retry Script

## Goal

Use if-else conditionals to make decisions based on a variable value.

### Evidence

#### Screenshot 1 — Content of `score-check.sh` with `score=85`

![Screenshot 1 — Content of `score-check.sh` with `score=85`](screenshots/Ass5-12.png)
---

#### Screenshot 2 — Output showing `Result: Pass`

![Screenshot 2 — Output showing `Result: Pass`](screenshots/Ass5-13.png)
---

#### Screenshot 3 — Content of `score-check.sh` with `score=55`

![Screenshot 3 — Content of `score-check.sh` with `score=55`](screenshots/Ass5-14.png)
---

#### Screenshot 4 — Output showing `Result: Retry`

![Screenshot 4 — Output showing `Result: Retry`](screenshots/Ass5-15.png)
---

### Notes

Answer the following in your own words:

**1. What is the purpose of if-else in Bash?**

An if-else statement allows the script to make decisions and execute different commands depending on whether a condition is true or false.
---

**2. What does `-ge` mean?**

-ge means greater than or equal to. It compares two numbers to determine whether the first value is at least as large as the second.
---

**3. Why should conditions be tested with different values?**

Testing different values helps confirm that every possible outcome works correctly and ensures the script behaves as expected.

---

**4. How can conditionals help in automation scripts?**

Conditionals allow scripts to respond intelligently to different situations, such as checking if a file exists, verifying a service is running, or handling errors automatically.
---

# Task 8 — Functions: Final Bash Automation Script

## Goal

Create a final Bash script using functions to organize reusable code.

### Evidence

#### Screenshot 1 — Content of `final-automation.sh`

![Screenshot 1 — Content of `final-automation.sh`](screenshots/Ass5-16.png)
---

#### Screenshot 2 — Output of `./final-automation.sh`

![Screenshot 2 — Output of `./final-automation.sh`](screenshots/Ass5-17.png)
---

#### Screenshot 3 — Output of `ls -lah` showing all created scripts

![Screenshot 3 — Output of `ls -lah` showing all created scripts](screenshots/Ass5-18.png)
---

### Notes

Answer the following in your own words:

**1. What is a function in Bash?**

Add your answer here.

---

**2. Why are functions useful in scripts?**

Add your answer here.

---

**3. Which functions did you create in this script?**

Add your answer here.

---

**4. How does this final script combine variables, arrays, loops, conditionals, files, and functions?**

Add your answer here.

---

# LinkedIn Post (Required)

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`Add your URL here`

---

#### Screenshot — Published LinkedIn post

Add your screenshot here.

---

# Submission Instructions

- Add all required screenshots in your submission
- Full name must be visible in required screenshots
- All script files must be created and run successfully
- Required notes must be answered clearly for every task
- Do not expose sensitive information (keys, passwords, credentials)

---

# Completion Checklist

- [ ] Task 1: Environment setup verified, workspace created (Screenshots 1–2, Notes answered)
- [ ] Task 2: First script created, executed, permissions verified (Screenshots 1–3, Notes answered)
- [ ] Task 3: Variables script created and run (Screenshots 1–2, Notes answered)
- [ ] Task 4: Arrays and loops script created and run (Screenshots 1–2, Notes answered)
- [ ] Task 5: Counter loop script created and run (Screenshots 1–2, Notes answered)
- [ ] Task 6: File validation script created and run (Screenshots 1–3, Notes answered)
- [ ] Task 7: Pass/Retry conditional script tested with both values (Screenshots 1–4, Notes answered)
- [ ] Task 8: Final automation script created and run (Screenshots 1–3, Notes answered)
- [ ] All scripts run without errors
- [ ] Full Name visible in all required screenshots
- [ ] LinkedIn post published and URL submitted
- [ ] No sensitive data exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://pravinmishra.com/dmi  
- 🎓 DevOps for Beginners (Udemy): https://www.udemy.com/course/devops-for-beginners-docker-k8s-cloud-cicd-4-projects/  
- 🎓 Agentic AI DevOps with Claude Code: https://www.udemy.com/course/ultimate-agentic-ai-devops-with-claude-code/  
- 🎓 DevOps with Claude Code: Terraform, EKS, ArgoCD & Helm: https://www.udemy.com/course/devops-with-claude-code-terraform-eks-argocd-helm/  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*