# **Optimizing GitHub Copilot with Custom Instructions and Prompt Files**

## **1.0 Introduction: From AI Pair Programmer to Custom Team Architect**

GitHub Copilot has evolved significantly from a simple code completion tool into a multifaceted, context-aware development platform.1 For high-performance software development teams, using Copilot "out-of-the-box" is no longer sufficient to harness its full potential.

The true value of this tool is unlocked not just by using AI, but by _customizing_ it to encode and automate a team's specific workflows, standards, and domain knowledge. This report will analyze the two main pillars of Copilot customization:

1.  **Custom Instructions:** Providing passive, persistent context and rules.3
2.  **Prompt Files:** Creating active, reusable tasks on demand.3

Understanding the interplay between these two features is key to transforming Copilot from a personal tool into a strategic, scalable enterprise asset.

## **2.0 The Two Pillars of Copilot Customization: A Comparative Analysis**

To understand these two customization mechanisms, we can use an analogy: "Push" and "Pull" information.

- The "Push" Model: Custom InstructionsThis mechanism pushes context and fixed rules into (nearly) all Copilot interactions. It passively shapes the AI's behavior.3 For example, a "pushed" instruction ensures Copilot always adheres to a specific coding standard without the developer needing to ask.
- The "Pull" Model: Prompt FilesThis mechanism allows a developer to pull a predefined workflow or tool on demand to perform a specific task.5 For example, the developer actively invokes a command to "create a new React component" based on the company's template.

The table below summarizes the key differences between these two features, based on analysis from multiple sources.3

**Table 1: Comparative Analysis of Copilot Customization Features**

**Feature**

**Primary Purpose**

**Activation Method**

**File Location (Default)**

**Scope**

**Usage Example**

**Custom Instructions**

Provide passive, persistent guidance and context.

**Automatic;** applied to relevant interactions.

1\. .github/copilot-instructions.md (Global)

2\. .github/instructions/NAME.instructions.md (Path-specific)

Workspace (Repository)

"Always use TSDoc-style comments for TypeScript functions."

**Prompt Files**

Define reusable, on-demand prompts for active tasks.

**Manual;** triggered by slash (/) command in chat (e.g., /code).

1\. .github/prompts/NAME.prompt.md (Workspace)

2\. VS Code User Profile (User)

Workspace or User

A /review command to perform a code review based on the team's checklist.

## **3.0 Mastering Custom Instructions: Enforcing Standards and Context**

Custom instructions are the mechanism for "teaching" Copilot about your project's specific rules and context.

### **3.1 Global Repository Context (.github/copilot-instructions.md)**

This is the simplest form of instruction: a single context file applied to the entire repository.6 This file should be considered the "single source of truth" for high-level project information.

**Example content for .github/copilot-instructions.md:**

- This project uses the Next.js framework with TypeScript and Tailwind CSS.
- All code must adhere to the company's style guide.
- Prefer functional components with Hooks over class-based components.
- Always include tests for new business logic.

### **3.2 Targeted Guidance with Path-Specific Instructions (.github/instructions/)**

For more granular control, Copilot allows for detailed instructions that apply only to specific files or directories.6

These files are placed in the .github/instructions/ directory and must have the .instructions.md extension. The key is that they must contain a YAML frontmatter block at the top, using the applyTo keyword with "glob" syntax to specify file patterns.6

**Example file .github/instructions/python-api.instructions.md:**

YAML

\---applyTo: "api/\*\*/\*.py"---- All Python API functions in this directory must be compatible with Python 3.11.- Type hints must be included for all function arguments and return values.- Use Pydantic models for input data validation.- Strictly adhere to PEP 8 standards.

### **3.3 The Additive Context System**

A key technical question is: "What happens if both a global copilot-instructions.md and a path-specific NAME.instructions.md file exist?"

The answer is that they do not override each other; instead, they operate on an _additive_ mechanism. The documentation indicates that "instructions from **both files are used**".6 When a developer works on a .py file in the api/ directory, Copilot will receive _both_ the global instructions (e.g., "use Next.js") and the Python-specific instructions (e.g., "use Pydantic"). This additive hierarchy is powerful for complex, multi-language repositories.

### **3.4 Verification**

A developer can verify that custom instructions are being applied by checking the "References list" of a response in Copilot Chat.6 If the instruction .md file appears in that list, its context was included in the prompt.

## **4.0 Architecting a Reusable Workflow Library with Prompt Files**

This is the "Pull" mechanism and the direct answer to the user's query about creating custom commands. Prompt files allow teams to define complex, reusable tasks and trigger them with simple slash commands.

### **4.1 Core Concept: From File to Slash Command**

This mechanism directly maps a file in the project to a new command in the Copilot chat interface. The process is as follows:

1.  A developer creates a file in the workspace at: .github/prompts/NAME.prompt.md. For example: .github/prompts/code.prompt.md.5
2.  Inside this file, they add a YAML frontmatter block.5
3.  The most important field in the frontmatter is name: your-command-name. For example: name: code.5
4.  On startup, the IDE (supported only in VS Code and JetBrains IDEs) 3 parses the .github/prompts/ directory.
5.  It automatically registers a _new custom slash command_ based on the name field's value.
6.  Now, when the user types / in the chat window, their custom /code command will appear alongside built-in commands like /explain and /tests.5

### **4.2 Anatomy of a .prompt.md File**

A prompt file consists of two parts: a YAML frontmatter Header for configuration and a Markdown Body for the prompt content.5

#### **4.2.1 YAML Frontmatter: Configuring the Command**

The frontmatter defines the command's behavior. The table below explains the key configuration fields.5

**Table 2: Prompt File YAML Frontmatter Configuration**

**Field**

**Description**

**Example Value**

**Strategic Importance**

name

The name of the command, used after the / in chat.

code

This _is_ your command. Keep it short, semantic, and unique.

description

A brief description of what the prompt does.

Generate code based on a free-form request.

This text appears in the command list. It is crucial "help text" for team adoption.

argument-hint

Optional placeholder text shown in the chat input box.

\[your code request\]

**Crucial** for the /code \[request\] scenario. It guides the user on what to type _after_ the command.

agent

The agent used to run the prompt.

ask, edit, agent (default)

A key choice. ask is for simple Q&A. agent 10 is for complex, multi-step tasks. edit is for _in-place modification_ of selected code.

model

The language model (LLM) to use.

GPT-4o

Allows you to _force_ a more powerful (or faster/cheaper) model for specific tasks, even if the user's default is different.

tools

A list of tools available to the prompt.

\['search/codebase'\]

This is the gateway to advanced functionality. Adding tools like search/codebase or githubRepo 5 allows your custom command to "see" other files in the repository.

#### **4.2.2 The Markdown Body: Prompt Design**

The body of the file (after the --- YAML block) is the _master prompt_.5 This is where prompt engineering principles 12 are applied to define the AI's role, task, constraints, and output format.

### **4.3 Handling User Input: "Guided" vs. "Free-form"**

This is the most complex part of the user's query. There are two distinct methods for handling user input after they type the command.

#### **Method 1: "Guided Input" with Named Variables (${input:...})**

Analysis of the generate-unit-tests.prompt.md example 11 shows the use of variables like ${input:function_name:Which function...}.

When a prompt file _contains_ these ${input:...} variables, it triggers an _interactive agent mode_. Copilot will _not_ execute immediately; instead, it will _ask the user_ each of these questions sequentially. This is for complex, multi-step tasks where specific parameters are needed.

#### **Method 2: "Free-form Input" (The \[request\] Model)**

The user's query (/code \[request\]) implies that a _single, unnamed string of text_ is passed in.

The documentation does not specify a particular variable (like ${request}) for this case.5 Therefore, the logic is: if _no_ ${input:...} variables are defined in the prompt body, Copilot will _automatically append the entire text string_ the user provides (the \[request\] part) to the _end_ of the prompt body.

This means the argument-hint field is purely for UI guidance, and the prompt body must be written to _expect_ the user's text to follow it.

#### **Method 3: Contextual Variables (${selection}, ${file})**

Additionally, prompt files can use automatic context variables, such as ${selection} 5, to automatically inject the text currently highlighted in the editor into the prompt.

## **5.0 Practical Implementation: An Essential Prompt File Library**

This section provides full code examples that developers can copy and use immediately.

### **5.1 Example 1: The User's Request - General Code Generator (/code \[request\])**

- **Goal:** To precisely answer the user's query, illustrating the "Free-form Input" method.
- **File:** .github/prompts/code.prompt.md
- **Source Code:\*\***name: code description: Generate clean, efficient code based on a free-form request. argument-hint: \[your code request\] model: GPT-4o\***\*You are a senior software architect. Your task is to generate clean,\*\***efficient, and well-documented code based on the user's request.\***\*Constraints:**
- Adhere to all modern best practices for the requested language.
- Include comments for complex logic.
- If the request is ambiguous, make reasonable assumptions and state them.
- Output only the code block, followed by a brief explanation.

**User Request:**

- **Usage:**

1.  In the Copilot chat window, type: /code create a React hook to fetch data using 'axios'
2.  **Analysis:** Copilot will take the contents of code.prompt.md and append the string "create a React hook to fetch data using 'axios'" to the end. This complete prompt is then sent to the LLM. The argument-hint field 5 guided the user, and the lack of ${input:...} variables triggered this "append" behavior.

### **5.2 Example 2: Advanced Unit Test Generator (/generate-tests)**

- **Goal:** To illustrate "Guided Input" 11 and the ${selection} context variable to create a standardized testing tool for the team.
- **File:** .github/prompts/generate-tests.prompt.md
- **Source Code: (Based on the official example from 11)\*\***name: generate-tests description: 'Generate unit tests for selected functions or methods' agent: 'agent'\***\*Task\*\***Analyze the selected function/method (from ${selection}) and generate a****comprehensive suite of unit tests to validate its behavior.****Target Function****${input:function_name:Which function/method do you want to test? (e.g., "validate_price")}\***\*Test Framework\*\***${input:framework:Which framework to use? (e.g., jest, pytest, rspec)}\***\*Test Generation Strategy**

1.  **Core Functionality Tests**

- Test the main purpose/expected behavior.

1.  **Input Validation Tests**

- Test with invalid input types, null values, boundaries.

1.  **Error Handling Tests**

- Test for expected exceptions being thrown.

Requirements

- **Use the project's existing testing framework and patterns.**
- Follow the AAA pattern: Arrange, Act, Assert.
- Mock external dependencies cleanly.
- **Usage:**

1.  In the editor, _highlight_ the function to be tested.
2.  In chat, type: /generate-tests
3.  **Analysis:** Copilot will trigger an agent 5 and respond: "Which function/method do you want to test?". It will automatically use your highlighted code via ${selection} 5 and then ask for the framework. This demonstrates the interactive workflow.

### **5.3 Example 3: In-place Documentation Generator (/add-docs)**

- **Goal:** To illustrate the edit agent 5 and the ${selection} variable 5 for a high-value, repetitive task.1
- **File:** .github/prompts/add-docs.prompt.md
- **Source Code:\*\***name: add-docs description: Generate and insert documentation for the selected code. agent: edit\***\*Analyze the code in the user's selection (${selection}).\*\***Generate comprehensive, well-formatted documentation for the code.\*\*
- For Python, use Google-style docstrings.
- For JavaScript/TypeScript, use TSDoc/JSDoc comments.
- For C#, use XML documentation comments.

Insert the generated documentation _directly_ above the selected code block.

- **Usage:**

1.  In the editor, _highlight_ the function or class that needs documentation.
2.  Open _Inline Chat_ (Ctrl+I or Cmd+I).
3.  Type: /add-docs and press Enter.
4.  **Analysis:** Copilot will not respond in the chat window. Instead, it will _directly edit your file_, inserting the documentation in place. The edit agent is a completely different workflow, turning the prompt file into a true refactoring tool.
