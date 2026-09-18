# LLM Code Evaluation Report — Assignment 0: Setup

> Copy this file to `LLM-Evaluation.md`, fill it in, and commit it. A0 is a **practice run** of the
> self-evaluation habit you'll use all term — the code is trivial, the workflow is the point.

## Student Information
- **Name**: Naman Tyagi
- **Date**: 18/09/2026
- **LLM Used**: Claude Sonnet 5 Medium

## Prompt Used

You are a friendly but precise teaching assistant for a graduate Java course. This is a SETUP
assignment (Assignment 0) — the only code change is that the student put their name into a Greeting
class so a test passes. Help me confirm my environment and workflow are correct. Do not rewrite my
code; just verify and explain.

Check the following and answer each briefly:
1. In my Greeting class, does message() return a greeting that includes MY name (not the placeholder
   "YOUR NAME HERE")? Quote the exact string it would return.
2. Given that, will the JUnit test messageNamesTheStudent PASS (it asserts the message is not blank
   and no longer contains "YOUR NAME HERE")? Will messageHasGreetingPrefix still pass?
3. In one or two plain-English sentences, explain what STUDENT_NAME and message() do, so I can say it
   in my own words during my code walk.
4. Did my tool output below indicate a clean run (tests green, coverage + checkstyle reports
   generated)? If something looks wrong, tell me what to re-check (JDK version, ./gradlew, etc.).

Here is my Greeting.java:
package edu.northeastern.setup;

/**
 * The one thing you edit this week: put your name in {@link #STUDENT_NAME} so the
 * app greets you by name. That is the whole assignment — the point is to get the
 * build, tests, LLM evaluation, and submission workflow working before Assignment 01.
 *
 * <p>This package is deliberately its own small world. It belongs to neither of the
 * two codebases you will actually build this term, and nothing here carries forward:
 * after this week you can forget it entirely. What carries forward is the layout it
 * sits in, and the commands you ran against it.
 */
public final class Greeting {

  private static final String STUDENT_NAME = "NAMAN TYAGI";

  private Greeting() {
    // utility class: no instances
  }

  /**
   * @return a greeting naming the student, e.g. {@code "Hello, Java! — Ada Lovelace"}.
   */
  public static String message() {
    return "Hello, Java! — " + STUDENT_NAME;
  }
}


Here is the output of my commands (paste what you saw):
PS D:\NEU\5010\SEP14\neu-5010-assignment00> ./gradlew run

> Task :run
Hello, Java! ∩┐╜ NAMAN TYAGI

BUILD SUCCESSFUL in 3s
2 actionable tasks: 1 executed, 1 up-to-date
PS D:\NEU\5010\SEP14\neu-5010-assignment00> ./gradlew test jacocoTestReport checkstyleMain

BUILD SUCCESSFUL in 3s
5 actionable tasks: 5 up-to-date

## What the LLM said
**1. Does message() include your name?**
Yes. `STUDENT_NAME` is `"NAMAN TYAGI"`, so the string is built from `"Hello, Java! — " + STUDENT_NAME`. Your `./gradlew run` output confirms it ends in `NAMAN TYAGI`. The exact string is:

`Hello, Java! — NAMAN TYAGI`

**2. Will the tests pass?**
- `messageNamesTheStudent` should pass. The message isn't blank and doesn't contain `"YOUR NAME HERE"`.
- `messageHasGreetingPrefix` should still pass, since you didn't touch the `"Hello, Java! — "` prefix. I can't see the test file, so this assumes it checks for `"Hello, Java!"` at the start.

**3. In your own words**
"`STUDENT_NAME` is a constant that holds my name, and `message()` returns the greeting `Hello, Java! — ` followed by that name. Tests check that the greeting isn't blank, no longer has the placeholder, and starts with the expected prefix."

**4. Was it a clean run?**
Mostly yes, with one thing to fix.

- **Green build:** Both commands ended in `BUILD SUCCESSFUL`. A failing test or checkstyle violation would have failed the build, so tests and checkstyle passed.
- **Up-to-date tasks:** The second command shows `5 up-to-date`, meaning Gradle skipped the tasks because nothing changed since an earlier successful run. That's normal, but the output doesn't show the tests running. To see them run and confirm the reports exist, do this:
  ```
  ./gradlew cleanTest test jacocoTestReport checkstyleMain
  ```
  Then look for these files:
    - `build/reports/tests/test/index.html`
    - `build/reports/jacoco/test/html/index.html`
    - `build/reports/checkstyle/main.html`
- **Garbled dash:** The `∩┐╜` in `Hello, Java! ∩┐╜ NAMAN TYAGI` is a bug worth fixing. That sequence is how a Windows console (CP437) displays the Unicode replacement character, so the em dash is probably getting mangled before it prints. The likely cause is that `Greeting.java` was saved in a non-UTF-8 encoding (e.g. Windows-1252) while Gradle compiles it as UTF-8. The tests probably still pass, but the returned string may not literally contain `—`. Try this:
    1. Re-save `Greeting.java` as UTF-8 in your editor (in VS Code, "Save with Encoding" → UTF-8).
    2. If `build.gradle` doesn't already set it, add `tasks.withType(JavaCompile) { options.encoding = 'UTF-8' }`.
    3. To fix the console display, run `chcp 65001` in PowerShell before `./gradlew run`.
    4. If the display is still garbled after that, see whether your template offers a UTF-8 option for the `run` task.

  If you'd rather sidestep it, a plain hyphen would work too, but check that the tests and any grader don't expect the em dash.

I can't verify your JDK version from this output, so run `java -version` and `./gradlew --version` and confirm they match what the course requires. Since the build ran with `./gradlew`, the wrapper itself is working.
## Your checklist
- **`./gradlew run` prints the greeting with my name?**: Yes
- **`./gradlew test` is green?**: Yes
- **Coverage + checkstyle reports generated?**: Yes
- **I can explain what `Greeting.message()` returns and why the test passes?**: Yes

## Reflection
[1–2 sentences: did anything in the setup trip you up (JDK, git, your editor, Gradle)? How did you
resolve it? This is the muscle you'll use for the graded weeks.]

Yes, the garbled em-dash in the output looked weird to me. I had to check and read what caused that specific problem. I also had to read about JUnit and how unit testing works in Java since I had never used it before.
