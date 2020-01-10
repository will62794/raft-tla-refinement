## January 10, 2020

Reproduced a simple case of refinement violation.

```
<Init>
/\ RM!ClientWrite(n1) = FALSE
/\ MaxLeader = (n1 :> Follower @@ n2 :> Follower @@ n3 :> Follower)
/\ MaxGlobalTerm = 0
/\ RM!Next = TRUE
/\  currentTerm = (n1 :> 0 @@ n2 :> 0 @@ n3 :> 0)
/\  immediatelyCommitted = {}
/\  log = (n1 :> <<>> @@ n2 :> <<>> @@ n3 :> <<>>)
/\  state = (n1 :> Follower @@ n2 :> Follower @@ n3 :> Follower)

<BecomeLeader>
/\ RM!ClientWrite(n1) = FALSE
/\ MaxLeader = (n1 :> Leader @@ n2 :> Follower @@ n3 :> Follower)
/\ MaxGlobalTerm = 1
/\ RM!Next = TRUE
/\  currentTerm = (n1 :> 1 @@ n2 :> 1 @@ n3 :> 0)
/\  immediatelyCommitted = {}
/\  log = (n1 :> <<>> @@ n2 :> <<>> @@ n3 :> <<>>)
/\  state = (n1 :> Leader @@ n2 :> Follower @@ n3 :> Follower)

<BecomeLeader>
/\ RM!ClientWrite(n1) = FALSE
/\ MaxLeader = (n1 :> Follower @@ n2 :> Leader @@ n3 :> Follower)
/\ MaxGlobalTerm = 2
/\ RM!Next = FALSE
/\  currentTerm = (n1 :> 1 @@ n2 :> 2 @@ n3 :> 2)
/\  immediatelyCommitted = {}
/\  log = (n1 :> <<>> @@ n2 :> <<>> @@ n3 :> <<>>)
/\  state = (n1 :> Leader @@ n2 :> Leader @@ n3 :> Follower)

<ClientRequest>
/\ RM!ClientWrite(n1) = "--"
/\ MaxLeader = (n1 :> Follower @@ n2 :> Leader @@ n3 :> Follower)
/\ MaxGlobalTerm = 2
/\ RM!Next = "--"
/\  currentTerm = (n1 :> 1 @@ n2 :> 2 @@ n3 :> 2)
/\  immediatelyCommitted = {}
/\  log = (n1 :> <<[term |-> 1]>> @@ n2 :> <<>> @@ n3 :> <<>>)
/\  state = (n1 :> Leader @@ n2 :> Leader @@ n3 :> Follower)
```

It is the case I expected to see i.e. a leader writing down an entry in a stale term when there is a newer leader. Plan to update the refinement mapping to try to handle this case. TLA+ Toolbox Trace Explorer is very helpful for debugging refinement checking violations.