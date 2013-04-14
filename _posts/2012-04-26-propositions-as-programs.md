---
layout: default
title: Propositions As Programs
---


The Curry-Howard correspondence says that propositions are types, and proofs are programs. I had been wondering if there is a simpler way to think about it, so I came up with this:

> Propositions are programs; proofs are "abstract interpretation" of them to True.

Here True is a meta-level truth value. A theorem prover is then an "abstract interpreter" which tries to evaluate the proposition to True. This computational process which derives True from the proposition, is called a judgment. Here I may have conflated the concept "abstract interpretation". I chose to use this term just because those two words "abstract" and "interpretation" conveys the general idea I hope to capture, but the term "abstract interpretation" here may not be what you use to think it is. I could have used "supercompilation", but I don't think the word conveys the concept very well.

Any way, this is a special kind of evaluation. It may be partial evaluation, supercompilation, or some sort of static analysis, whatever you call it. It can also take human inputs interactively, as long as it is semantic-preserving -- it returns True whenever an actual interpreter would return True. But it is a lot more efficient, because unlike a normal interpreter, it takes shortcuts (e.g. induction hypotheses). If it thus evaluates to True, then the proposition is proved. This is much like fortune-telling. It immediately tells you the result that an actual interpreter would eventually give you. It has a limited number of basic "reduction operations" such as unfolding, induction and rewriting, so we can record reduction sequences as "proofs", and we can verify them later on.

This seems to match what we have been doing with proof assistants like Coq, and possibly a more natural way of thinking about this kind of theorem proving. I feel that the propositions in Coq are a little far-fetched to be called "types" of the proof terms (note: not to be confused with tactics). For example, we can have a proof term like

    fun (n' : nat) (IHn' : n' + 0 = n') => ...

Is n' + 0 = n' the type of IHn'? Well, you can call it whatever you like, but calling it a "type" doesn't feel natural at all. What we want is just a way to bind an equation to a name, so that we can refer to it. It feels better if we just think of propositions as programs with boolean return types and the proof terms reduction sequences of them into True. If you take a look at the proof terms of Coq, you may find that this is the case. For example, take a look at this simple theorem and the tactics that prove it:

    Theorem plus_0_r : forall n:nat, n + 0 = n.
    Proof.
      intros n. induction n as [| n'].
      reflexivity.
      simpl. rewrite -> IHn'. reflexivity.  Qed.

 They produce the following proof term:

    plus_0_r = 
    fun n : nat =>
    nat_ind (fun n0 : nat => n0 + 0 = n0) eq_refl
      (fun (n' : nat) (IHn' : n' + 0 = n') =>
       eq_ind_r (fun n0 : nat => S n0 = S n') eq_refl IHn') n
         : forall n : nat, n + 0 = n

You may think of this proof term as a program with the theorem as its type, but you can also think of it as a reduction of the program n + 0 = n to True, where n is a natural number. It is saying: "Do an induction where the first branch executes an equivalence judgment, and the other branch does an unfolding, a rewriting using the induction hypothesis, and an equivalence judgment." I think the second way of thinking of it is much more natural.

This interpretation can also explain the operation of Boyer-Moore style theorem provers (e.g. ACL2), as this is almost exactly how they work. They just don't normally output a machine-verifiable reduction sequence.

You may have noticed that we have an important difference from the orginal Curry-Howard correspondence here:

> Proofs are no longer considered programs.

At least proofs are not object-level programs. They are the "operation histories" in the abstract interpreter, which are at the meta-level. We can give this operation history to an independent verifier, so that it can "replay" the abstract interpretation and verify the correctness of the proof.

Alternatively, if you consider the verifier as an interpreter then proofs are its input programs. In this sense you can also say that proofs are programs for the proof verifier, and both propositions and proofs can be considered programs. But they are programs at two different semantic levels: the proofs are at a higher level than the propositions. This is very different from Curry-Howard.

Thus we have arrived at a unified way of thinking about the two very different styles of theorem proving: Curry-Howard correspondence (as in Coq), and Boyer-Moore (as in ACL2).
