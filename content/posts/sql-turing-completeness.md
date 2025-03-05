---
title: "SQL: Turing Completeness"
date: 2025-03-05T00:00:00+00:00
author: Talha Altinel
description: "Let's make a Turing Machine Simulation in SQL"
tags:
- sql
- postgresql
- turing-machine
slug: sql-turing-completeness
canonicalURL: https://wormholerelays.com/posts/sql-turing-completeness
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
draft: false
cover:
  image: EFkasdASFKVCCN.webp
  alt: turing-machine-image
---

So I have been dying to write this blog post for a while, I feel great that I had some time to focus on this topic. Initially my primary reason to write this post is
I see the great negligence from very senior developers when they hear "SQL" and I came across a few very senior interviewers who said "SQL is not even programming language, we use ORMs, why bother learning something that is not even a programming language(they mean SQL)" so the main purpose of this post is to shed some light over ignorance/incompetence with the proof of "SQL indeed is really a programming language" and why you must learn and practice it just like other programming languages.

First of all, we need to talk about what makes a language a programming language. It is actually very simple, "Turing completeness" is what makes a language a programming language. But what is "Turing completeness"? I am glad you asked, short answer is you need to understand "Turing machines" before we talk about "Turing completeness". What is a "Turing machine"?

Turing machine is a theoretical device that was invented by Alan Turing (check on your £50 bank note bills, he is right there!) in order to understand computation. Turing machine has 
- Long tape divided into cells (think as an infinite long strip of paper)
- A head that can read symbols from long tape then write new symbols then move left/right or stand still
- The machine state (think possible "modes" that machine can be in)
- The transition rules (a set of rules that dictate the flow of current state to new states)

Now, back to "Turing completeness", a system is Turing complete if it can simulate any Turing machine. This means it can solve any problem that a Turing machine can solve.

So if I write how to solve a niche problem with Turing machine simulation in SQL, I will prove that "SQL is indeed a programming language". Notice the word that we are saying "simulation" because a real Turing machine has infinite memory and CPU that never halts, it is a theoretical device which can be implemented in any real programming languages.

## Turing Machine Simulation in SQL

First of we are going to need our machine. And machine needs to have initial state, accept state and reject state and blank symbol and max steps to halt.

```sql
CREATE TABLE machine (
    initial_state VARCHAR(64) NOT NULL,
    accept_state VARCHAR(64) NOT NULL,
    reject_state VARCHAR(64) NOT NULL,
    blank_symbol VARCHAR(1) NOT NULL DEFAULT '_',
    max_steps INTEGER NOT NULL DEFAULT 1000
);
```

Secondly we need to know transition rules which is the state diagram for any sort of Turing machine.

```sql
CREATE TABLE transition_rules (
    state VARCHAR(64) NOT NULL,
    new_state VARCHAR(64) NOT NULL,
    read_symbol VARCHAR(1) NOT NULL,
    write_symbol VARCHAR(1) NOT NULL,
    move_direction VARCHAR(1) NOT NULL CHECK (move_direction IN ('L', 'R', 'N'))
);
```

Before we go any further, I will add procedures for machine and transition rules so that in a capsulated procedure(program) which will contain different transition rules for 1 machine, we can use them easily. Machine is always singleton which needs to be cleaned everytime it is set, Transition rules need to be only cleaned in a capsulated procedure(program).

```sql
-- initialize_machine initializes the Turing machine for a specific program
CREATE OR REPLACE PROCEDURE initialize_machine(
    initial_state VARCHAR(64),
    accept_state VARCHAR(64),
    reject_state VARCHAR(64),
    blank_symbol VARCHAR(1) DEFAULT '_',
    max_steps INTEGER DEFAULT 1000
) AS $$
BEGIN
    DELETE FROM machine; -- clear
    
    INSERT INTO machine VALUES (initial_state, accept_state, reject_state, blank_symbol, max_steps);
END;
$$ LANGUAGE plpgsql;

-- add_transition_rule adds transition rule for a specific program
CREATE OR REPLACE PROCEDURE add_transition_rule(
    state VARCHAR(64),
    new_state VARCHAR(64),
    read_symbol VARCHAR(1),
    write_symbol VARCHAR(1),
    move_direction VARCHAR(1)
) AS $$
BEGIN
    INSERT INTO transition_rules VALUES (state, new_state, read_symbol, write_symbol, move_direction);
END;
$$ LANGUAGE plpgsql;
```

In Turing machines, the standard convention is that the head reads the current symbol, then writes a new symbol, and finally moves. This is known as the "read-write-move" sequence for each step of the computation.

Now we will define a function for running steps(iterations) that follows "read-write-move" algoritm, to be able to understand arguments and parameters. I will explain the logic flow.

- Take current state, accept state and reject state
- If current state is accept state or reject state, we return current state and halt
- Take the tape(it is long text), take the position
- We use pos to determine if we are inside of the tape, we take read the symbol from the tape. If outside of tape, we read the symbol as blank. Remember string indices start from 1 in SQL
- Query transition rule that matches our current_state and read symbol from tape
- If transition rule is not found, return halted as true
- Write the new symbol(indicated from transition rule) to the tape
- Increment or decrement the position according to move direction(indicated from transition rule)
- Finally, return new state and the modified tape and new position and halted status

The reason why we use a function over procedure is obvious one, we need to return halted status and others since they will be used in a loop to determine the halting point.

```sql
-- run_step executes a single step of machine
CREATE OR REPLACE FUNCTION run_step(
    current_state VARCHAR(64),
    accept_state VARCHAR(64),
    reject_state VARCHAR(64),
    tape TEXT,
    pos INTEGER,
    blank VARCHAR(1)
) RETURNS TABLE (
    new_state VARCHAR(64),
    new_tape TEXT,
    new_pos INTEGER,
    halted BOOLEAN
) AS $$
DECLARE
    tape_length INTEGER;
    symbol VARCHAR(1);
    rule RECORD;
BEGIN
    -- check if it is a final state
    IF current_state = accept_state OR current_state = reject_state THEN
        RETURN QUERY SELECT current_state, tape, pos, TRUE;
        RETURN;
    END IF;

    tape_length := length(tape);

    -- get the current symbol
    IF pos < 1 OR pos > tape_length THEN
        symbol := blank;
    ELSE
        symbol := substr(tape, pos, 1);
    END IF;
    
    -- query transition rule
    SELECT * INTO rule FROM transition_rules tr
    WHERE tr.state = current_state AND tr.read_symbol = symbol
    LIMIT 1;
    
    IF rule IS NULL THEN -- no rule found, halt
        RETURN QUERY SELECT current_state, tape, pos, TRUE;
        RETURN;
    END IF;
 
    IF pos < 1 THEN -- extend tape left
        tape := rule.write_symbol || tape;
        pos := 1;
    ELSIF pos > tape_length THEN -- extend tape right
        tape := tape || rule.write_symbol;
    ELSE
        tape := substr(tape, 1, pos-1) || rule.write_symbol || substr(tape, pos+1);
    END IF;
    
    IF rule.move_direction = 'L' THEN
        pos := pos - 1;
    ELSIF rule.move_direction = 'R' THEN
        pos := pos + 1;
    END IF;
    
    RETURN QUERY SELECT rule.new_state, tape, pos, FALSE;
END;
$$ LANGUAGE plpgsql;
```

And now we are going to be define our event loop, it will be a procedure since it doesn't need to return anything, however I will need to debug or show outputs to my fellow readers at some point so I will define a new machine steps table to record every step of my machine for debugging purposes.

```sql
CREATE TABLE machine_steps (
    step INTEGER NOT NULL,
    state VARCHAR(64) NOT NULL,
    tape TEXT NOT NULL,
    position INTEGER NOT NULL,
    halted BOOLEAN NOT NULL DEFAULT FALSE
);
```

Our algoritm for running machine is relatively simple.

- Take tape which is a text as argument
- Assign initial state, accept state, reject state, blank symbol and max steps from machine
- Record the first machine step as 0
- While it is not halted and step is less than max steps, start the loop
- In the loop, increment the step, execute one machine step and assign new state, new tape, new position and halted status
- In the loop, record the machine step byproducts
- After loop ends, if steps has reached max steps and not halted, set last machine step with timeout state and halted status as halted

Fun fact, max steps is defined in our program to overcome most famous theoritical problem in computer sciences (aka Halting problem). The Halting Problem asks whether there exists a general algorithm that can determine, for any arbitrary program and input, whether that program will eventually halt (terminate) or run forever. Turing proved that no such algorithm can exist - it's mathematically impossible to create a procedure that can always correctly predict whether an arbitrary program will halt.

```sql
-- run_machine runs machine
CREATE OR REPLACE PROCEDURE run_machine(t TEXT) AS $$
DECLARE
    tape TEXT := COALESCE(t, '');
    state VARCHAR(64);
    position INTEGER := 1;
    accept_state VARCHAR(64);
    reject_state VARCHAR(64);
    blank_symbol VARCHAR(1);
    max_steps INTEGER;
    halted BOOLEAN := FALSE;
    step INTEGER := 0;
BEGIN
    DELETE FROM machine_steps; -- clear
    
    -- read machine state
    SELECT m.initial_state, m.accept_state, m.reject_state, m.blank_symbol, m.max_steps
    INTO state, accept_state, reject_state, blank_symbol, max_steps
    FROM machine m;
    
    -- record machine step
    INSERT INTO machine_steps (step, state, tape, position, halted)
    VALUES (step, state, tape, position, halted);
    
    WHILE NOT halted AND step < max_steps LOOP
        step := step + 1;
        
        -- execute one machine step and directly assign results to main variables
        SELECT fn.new_state, fn.new_tape, fn.new_pos, fn.halted
        INTO state, tape, position, halted
        FROM run_step(state,  accept_state, reject_state, tape, position, blank_symbol) fn;
        
        -- record one machine step
        INSERT INTO machine_steps (step, state, tape, position, halted)
        VALUES (step, state, tape, position, halted);
    END LOOP;
    
    -- check if we timed out
    IF step = max_steps AND NOT halted THEN
        UPDATE machine_steps SET state = 'TIMEOUT', halted = TRUE
        WHERE step = max_steps;
    END IF;
END;
$$ LANGUAGE plpgsql;
```

With this our Turing Machine is complete!!! now how do we actually run it? well, you sort of need a state diagram that solves a problem! In the end, if you don't have a problem, why do you need a machine??

I have come up with a palindrome recognizer state diagram below, let me explain briefly.

![Palindrome-Turing-Machine](/turing-machine-palindrome.png)

```mermaid
stateDiagram-v2
    [*] --> q0
    
    q0 --> q1: 0 / _, R
    q0 --> q2: 1 / _, R
    q0 --> yes: _ / _, N
    
    q1 --> q1: 0 / 0, R
    q1 --> q1: 1 / 1, R
    q1 --> q3: _ / _, L
    
    q2 --> q2: 0 / 0, R
    q2 --> q2: 1 / 1, R
    q2 --> q4: _ / _, L
    
    q3 --> q5: 0 / _, L
    q3 --> no: 1 / 1, N
    q3 --> yes: _ / _, N
    
    q4 --> q5: 1 / _, L
    q4 --> no: 0 / 0, N
    q4 --> yes: _ / _, N
    
    q5 --> q5: 0 / 0, L
    q5 --> q5: 1 / 1, L
    q5 --> q0: _ / _, R
    
    yes --> [*]: Accepted
    no --> [*]: Rejected
```

Walking through the example with the string "101":

| Step | State | Position | Read | Action                | New State | Tape  | New Position |
|------|-------|----------|------|-----------------------|-----------|-------|--------------|
| 1    | q0    | 1        | `1`  | Write `_`, Move Right | q2        | `_01` | 2            |
| 2    | q2    | 2        | `0`  | Write `0`, Move Right | q2        | `_01` | 3            |
| 3    | q2    | 3        | `1`  | Write `1`, Move Right | q2        | `_01` | 4            |
| 4    | q2    | 4        | `_`  | Write `_`, Move Left  | q4        | `_01` | 3            |
| 5    | q4    | 3        | `1`  | Write `_`, Move Left  | q5        | `0`   | 2            |
| 6    | q5    | 2        | `0`  | Write `0`, Move Left  | q5        | `0`   | 1            |
| 7    | q5    | 1        | `_`  | Write `_`, Move Right | q0        | `0`   | 2            |
| 8    | q0    | 2        | `0`  | Write `_`, Move Right | q1        | `___` | 3            |
| 9    | q1    | 3        | `_`  | Write `_`, Move Left  | q3        | `___` | 2            |
| 10   | q3    | 2        | `_`  | Write `_`, Stay       | yes       | `___` | 2            |


At yes state - The machine halts and accepts the string. Below is the palindrome program in SQL procedure which uses turing machine to solve the palindrome problem.

```sql
-- run_palindrome_program runs the palindrome program in Turing machine
CREATE OR REPLACE PROCEDURE run_palindrome_program(t TEXT) AS $$
BEGIN
    DELETE FROM transition_rules; -- clear
    
    CALL initialize_machine('q0', 'yes', 'no');
    
    -- q0: read left most symbol and move right side
    CALL add_transition_rule('q0', 'q1', '0', '_', 'R'); 
    CALL add_transition_rule('q0', 'q2', '1', '_', 'R'); 
    CALL add_transition_rule('q0', 'yes', '_', '_', 'N'); 
    
    -- q1: 0 was at the beginning, now go to the right-most end
    CALL add_transition_rule('q1', 'q1', '0', '0', 'R'); 
    CALL add_transition_rule('q1', 'q1', '1', '1', 'R'); 
    CALL add_transition_rule('q1', 'q3', '_', '_', 'L'); 
    
    -- q2: 1 was at the beginning, now go to the right-most end
    CALL add_transition_rule('q2', 'q2', '0', '0', 'R'); 
    CALL add_transition_rule('q2', 'q2', '1', '1', 'R'); 
    CALL add_transition_rule('q2', 'q4', '_', '_', 'L'); 
    
    -- q3: check if last symbol matches for 0 at the beginning
    CALL add_transition_rule('q3', 'q5', '0', '_', 'L'); 
    CALL add_transition_rule('q3', 'no', '1', '1', 'N'); 
    CALL add_transition_rule('q3', 'yes', '_', '_', 'N'); 
    
    -- q4: check if last symbol matches for 1 at the beginning
    CALL add_transition_rule('q4', 'q5', '1', '_', 'L'); 
    CALL add_transition_rule('q4', 'no', '0', '0', 'N'); 
    CALL add_transition_rule('q4', 'yes', '_', '_', 'N'); 
    
    -- q5: now go back to the left-most end
    CALL add_transition_rule('q5', 'q5', '0', '0', 'L'); 
    CALL add_transition_rule('q5', 'q5', '1', '1', 'L'); 
    CALL add_transition_rule('q5', 'q0', '_', '_', 'R'); 
    
    CALL run_machine(t);
END;
$$ LANGUAGE plpgsql;
```

```shell
turing_machine=# call run_palindrome_program('101');
CALL
turing_machine=# select * FROM machine_steps;
 step | state | tape | position | halted 
------+-------+------+----------+--------
    0 | q0    | 101  |        1 | f
    1 | q2    | _01  |        2 | f
    2 | q2    | _01  |        3 | f
    3 | q2    | _01  |        4 | f
    4 | q4    | _01_ |        3 | f
    5 | q5    | _0__ |        2 | f
    6 | q5    | _0__ |        1 | f
    7 | q0    | _0__ |        2 | f
    8 | q1    | ____ |        3 | f
    9 | q3    | ____ |        2 | f
   10 | yes   | ____ |        2 | f
   11 | yes   | ____ |        2 | t
(12 rows)
```

If you want do try on your own, you can clone my repository. Let's try `1001` for example.
```
git clone https://github.com/mrwormhole/turing-machine-in-sql
docker compose up -d --build
docker exec -it postgres-turing psql -U turing -d turing_machine
turing_machine=# select * FROM machine_steps;
turing_machine=# call run_palindrome_program('1001');
turing_machine=# select * FROM machine_steps;
```

```
------+-------+-------+----------+--------
    0 | q0    | 1001  |        1 | f
    1 | q2    | _001  |        2 | f
    2 | q2    | _001  |        3 | f
    3 | q2    | _001  |        4 | f
    4 | q2    | _001  |        5 | f
    5 | q4    | _001_ |        4 | f
    6 | q5    | _00__ |        3 | f
    7 | q5    | _00__ |        2 | f
    8 | q5    | _00__ |        1 | f
    9 | q0    | _00__ |        2 | f
   10 | q1    | __0__ |        3 | f
   11 | q1    | __0__ |        4 | f
   12 | q3    | __0__ |        3 | f
   13 | q5    | _____ |        2 | f
   14 | q0    | _____ |        3 | f
   15 | yes   | _____ |        3 | f
   16 | yes   | _____ |        3 | t
```

## Final Words

Since we proved SQL is indeed a programming language, I want to stress a final point. We have utilized "pl/pgsql" which is the procedural language of PostgreSQL. During ANSI-SQL(SQL-86/SQL-89), SQL was not turing complete at that time because it lacked recursive structures such as loops. SQL-99 added "WITH RECURSIVE" to do while loops and procedural elements(WHEN/CASE etc). In today's world, every production database is minimum SQL-99 compliant. Even sqlite's SQL dialect is turing complete.

Lastly, even if we lived before 1999, entire world's data is running since 1986. Why would people take pride of learning ORM abstractions rather than learning fundamentals of existing databases. And ORM abstractions will surely change more often and not provide the full feature sets of what you can achieve. It is just wishful thinking to ignore SQL and treat it like a chore.