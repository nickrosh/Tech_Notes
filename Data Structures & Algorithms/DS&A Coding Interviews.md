
The Data Structures and Algorithms Coding interview is the classic software engineer interview that is given at all top companies and startups. You must display additional competency beyond just being able to quietly solve the algorithm problem like in Leetcode. As in real software engineering, you must display your communication skills and attention to detail to be viewed as a strong hire for the company.

## Interview Rubric

Across top tech companies, coding interview evaluation criteria actually does not differ to a great extent. While the exact terms used in the rubric could be different, the dimensions evaluated are roughly similar. Generally across top companies, coding interview evaluation rubrics can be split broadly into four categories:
1. **Communication**: Does the candidate make clarifications, communicate their approach and explain while coding
2. **Problem Solving**: Does the candidate show they understand the problem and are able to come up with a sound approach, conduct trade-offs analysis and optimize their approach
3. **Technical Competency**: How fast and accurate is the implementation? Were there syntax errors?
4. **Testing**: Was the code tested for common and corner cases? Did they self-correct bugs?

There are generally two general methods of candidate scoring: provide a score for each dimension, or provide an overall score across all dimensions. Regardless of the method used, the scoring bands are generally:
- Strong Hire
- Hire
- No Hire
- Strong No Hire

#### 1. Communication

Basic communication signals:
- Asks appropriate Clarifying questions
- Communicates approach, rationale, and tradeoffs
- Constantly communicating, even when coding
- Well organized, succinct, clear communication

| Score           | Overall Evaluation                                                                                                                                                                                                                                                                            |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Strong Hire     | Throughout the interview, communication was thorough, well-organized, succinct, and clear in terms of thought process, including how they understand the question, their approach, trade-offs. Interview had no challenge following and understanding the candidate's through process at all. |
| Leaning Hire    | Throughout the interview, communication was sufficient, clear, and organized. However, the interview had to ask follow-up questions to understand the candidate on curtain aspects such as their approach or through process                                                                  |
| Leaning no Hire | Throughout the interview, communication was (1 or more of the following): 1. Insufficient (e.g. jumped into coding without explaining), 2. Disorganized or unclear, interviewer had difficulty following the candidate's thought process.                                                     |
| Strong no Hire  | Could not communicate with any clarity or stayed silent even when addressed by the interview. Interviewer had extreme difficulty following the candidate's thought process.                                                                                                                   |

#### 2. Problem Solving

Basic problem solving signals:
- Understand the problem quickly by asking good clarifying questions
- Approached the problem systematically and logically
- Was able to come up with an optimized solution
- Determined [Time & Space Complexity](Time%20&%20Space%20Complexity.md) accurately
- Did not require any major hints from the interviewer

Advanced problem solving signals:
- Came up with multiple solutions
- Explained trade-offs of each solution clearly and correctly, concluded on which of them are most suitable for the current scenario
- Had time to discuss follow up problems/extensions

| Score           | Overall Evaluation                                                                                                                  |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| Strong Hire     | No trouble achieving all basic problem solving signals and did so with enough time to achieve most advanced problem solving signals |
| Leaning Hire    | Managed to achieve all basic problem solving signals but did not have sufficient time to achieve advanced problem solving signals.  |
| Leaning No Hire | Showed only some basic problem solving signals, failing to achieve the rest.                                                        |
| Strong No Hire  | Unable to solve the problem or did it without much explanation of their thought process. Approach was disorganized and incorrect.   |

#### 3. Technical Competency

Basic technical competency signals:
- Translates discussed solution into working code with minimal to no bugs
- Clean and straightforward implementation with no syntax errors and unnecessary code, good coding practices e.g. DRY, uses proper abstractions
- Neat coding style (proper indentation, spacing, variable naming, etc.)

Advanced technical competency signals:
- Compares several coding approaches
- Demonstrates strong knowledge of language constructs and paradigms

| Score           | Overall Evaluation                                                                                                                                      |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Strong Hire     | Demonstrated basic and advanced competency signals effortlessly                                                                                         |
| Leaning Hire    | Demonstrated only basic technical competency signals, with some difficulty seen in translating approach to code. Suboptimal usage of language paradigms |
| Leaning No Hire | Struggled to produce a working solution in code. Multiple syntax errors and bad use of language paradigms.                                              |
| Strong No Hire  | Could not produce a working solution in code. Major syntax errors and very bad use of language paradigms.                                               |

#### 4. Testing

Testing signals:
- Came up with more typical cases and tested their code against it
- Found and handled corner cases
- Identified and self-corrected bugs in code
- Able to verify correctness of the code in a systematic manner (e.g. acting like a debugger and stepping through each line, updating the program's state at each step)

| Score           | Overall Evaluation                                                                                               |
| --------------- | ---------------------------------------------------------------------------------------------------------------- |
| Strong Hire     | Demonstrated testing signals effortlessly.                                                                       |
| Leaning Hire    | Had some difficulty demonstrating testing signals, such as not being able to identify all relevant corner cases. |
| Leaning No Hire | Conducted testing but did not handle corner cases. Not able to identify or correct bugs in code.                 |
| Strong No Hire  | Did not test code against typical cases. Did not spot glaring bugs in the code and announced they are done.      |


## Best Practices

Before the interview, make sure you have pen and paper to jot down notes and visualizations during the interview. Also make sure to have your self introduction and final questions to ask prepared beforehand. If it is a virtual interview, make sure you have good internet and a webcam. During the interview, the following are the best practices to follow:

#### 1. Make a good self introduction at the start of the interview
- Introduce yourself in a few sentences under a minute or 2.
- Sound enthusiastic!
- Do not spend too long on your self-introduction as you will have less time left to code

#### 2. Upon Receiving the Question, Make Clarifications
**Do not jump into coding right away.** Coding questions tend to be vague and underspecifed on purpose to allow the interview to gauge the candidate's attention to detail and carefulness. Ask at least 2-3 clarifying questions.
- Paraphrase and repeat the question back at the interviewer
- Clarify assumptions
	- A [Trees](Data%20Structures/Trees.md) like diagram could very well be a [Graph](Data%20Structures/Graphs.md) that allows for cycles and a naive recursive solution would not work. Clarify if the given diagram is a tree or a graph.
	- Can you modify the original [Array](Data%20Structures/Arrays.md)/Graph/Data Structure in any way?
	- How is the input stored?
	- If you are given a dictionary of words, is it a list of strings or a [Trie](Data%20Structures/Tries.md)?
	- Is the input array sorted? (e.g. for deciding between linear and [Binary Search](Algorithms/Binary%20Search.md))
- Clarify Input value range. Inputs: how big and what is the range?
- Clarify input value format. Values: Negative? Floating points? Empty? Null? Duplicates? Extremely Large?
- Work through a simplified example to ensure you understood the question. E.g. you are asked to write a palindrome checker, before coding, come up with simple test cases like KAYAK and ask the interview if those cases line up with their expectations
- Do not jump into coding right away or before the interviewer gives you the green light to do so.

#### 3. Work out and optimize your approach with the interviewer
The **Worst** thing you can do next is jump straight to coding. Interviews expect there to be some time for a two-way discussion on the correct approach to take for the question, including analysis of the time and space complexity. This discussion can range from a few minutes to up to 5-10 minutes depending on the complexity of the question. This also gives interviewers a chance to provide you with hints to guide you towards an acceptable solution.
- If you get stuck on the approach or optimization, Use a [structured way](Solving%20DS&A%20Questions.md) to solve such problems.
- Explain a few approaches that you could take at a high level (don't go too much into implementation details). Discuss the tradeoffs of each approach of each approach with your interviewer as if the interviewer was your coworker and you all are collaborating on a problem. For example, with 2-Sum, you can either do Time = $O(n^2)$ Space = $O(1)$ or Time = $O(n)$ Space = $O(n)$. Discuss both solutions and their tradeoffs and conclude which one is better (typically the one with better time complexity).
- State and explain the [Time & Space Complexity](Time%20&%20Space%20Complexity.md) of your proposed approach(s).
- Agree on the most ideal approach and optimize it. Identify repeated/duplicated/overlapping computations and reduce them with caching.
- Do not jump into coding right away or before the interviewer gives you the green light to do so
- Do not ignore any piece of information given
- Do not appear unsure about your approach or analysis

#### 4. Code out your solution while talking through it
- Only start coding after you have explained your approach and the interviewer has given you the green light.
- Explain what you are trying to achieve as you are coding/writing. Compare different coding approaches where relevant. In doing so, demonstrate mastery of your chosen language.
- Code/write at a reasonable speed so you can talk through it, but not too slow.
- Write actual compilable, working code where possible, not pseudocode
- Write clean, straightforward, and neat code with as few syntax errors/bugs as possible. Always for a clean, straightforward implementation than a complex, messy one.
- Use variable names that explain your code. Good variable names are important because you need to explain your code to the interviewer. It's better to use long variable names that explain themselves.
- Ask for permission to use trivial functions without having to implement them. E.g. `reduce, filter, min, max`.
- Write in a modular fashion, going from higher-level functions and breaking them down into smaller helper functions
- If you are cutting corners in code, state that out loud to your interviewer and say what you would  do in a non-interview setting (no time constraints)
- (Onsite Whiteboarding only: practice whiteboard space management)
- Do not interrupt your interviewer when they are talking. Usually when they are talking, they are trying to give you hints or steer you in the right direction
- Do not spend too much time writing comments
- Do not repeat yourself
- Do not use bad variable names like single letters
- Do not copy and paste code without checking (e.g. some variables might need to be renamed after pasting).

#### 5. After coding, check your code and add test cases
Once you are done coding, do not announce that you are done. Interviewers expect you to start scanning for mistakes and adding test cases to improve on your code.
- Scan through your code for mistakes, such as off-by-one errors
- Brainstorm edge cases with the interviewer and add additional test cases
- Step through your code with those test cases
- Look out for places where you can refactor
- Reiterate the time and space complexity of your code. This allows you to remind yourself to spot issues without your code that could deviate from the original time and space complexity.
- Do not immediately announce that you are done coding. Do the above first!
- Do not argue with the interviewer. They may be wrong but that is very unlikely given they are familiar with the question.

#### 6. At the end of the interview, leave a good impression
- Ask good final questions that are tailored to the company
- Thank the interviewer
- Do not end the interview without asking any questions.
