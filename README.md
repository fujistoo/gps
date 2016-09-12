## Machine Problem – GPS
Based of **Peter Norvig**'s Paradigms of Artificial Intelligence Programming, the improved General Problem Solver (GPS) implementation replaced the global `*state*` variable with local state variables. This allows GPS to attempt actions that might make one or more goals unreachable, yet backtrack to a previous state (i.e., stored in a recursive call) where alternative actions could be tried. Unfortunately, it still does not guarantee that a solution will be found whenever one is possible.

To demonstrate the problem, we added another operator to the front of the `*school-ops*` list and turned debugging output on:
<pre>
  <code>
> (use (push (op 'taxi-son-to-school
               :preconds '(son-at-home have-money)
               :add-list '(son-at-school)
               :del-list '(son-at-home have-money))
               *school-ops*))
> (setdebug :gps)
  </code>
</pre>

Now, consider the problem of getting the child to school without using any money:
<pre>
  <code>
> (gps '(son-at-home have-money car-works)
       '(son-at-school have-money))

Goal: SON-AT-SCHOOL
Consider: TAXI-SON-TO-SCHOOL
  Goal: SON-AT-HOME
  Goal: HAVE-MONEY
Action: TAXI-SON-TO-SCHOOL
Goal: HAVE-MONEY
Goal: HAVE-MONEY
Goal: SON-AT-SCHOOL
Consider: TAXI-SON-TO-SCHOOL
  Goal: SON-AT-HOME
  Goal: HAVE-MONEY
Action: TAXI-SON-TO-SCHOOL
NIL
  </code>
</pre>

The first five lines of output succesfully solve the `son-at-school` goal with the `TAXI-SON-TO-SCHOOL` action. The next line shows an unsuccesful attempt to solve the `have-money` goal. The next step is to try the other ordering. This time, the `have-money` goal is tried first, and succeeds. Then, the `son-at-school` goal is achieved again by the `TAXI-SON-TO-SCHOOL` action. But the check for consistency in `achieve-each` fails, and there are no repairs available. The goal fails, even though there is a valid solution: driving to school.

The program is updated so that it keeps track of the remaining goals and does not get stuck considering only one possible operation when others will eventually lead to the goal. The `achieve` will take an extra argument indicating the goals that remain to be achieved after the current goal is achieved. `achieve` should succeed only if it can achieve the current goal and also `achieve-all` the remaining goals.
