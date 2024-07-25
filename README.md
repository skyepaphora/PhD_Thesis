## Task completion

#### June

* Got rid of unnecessary transpositions, this took quite a bit of algebra
* code is now 2.25 times faster according to profile... I still don't trust battery saver
* plots look good, seem basically identical to the old function's output when I set a seed
* code now compatible with GUMP-2s!! :)

#### July

* installed BSS packages, read through documentation
* recreated cocktail party example, put together a little demo? IDK
* walked through different algorithms, we've only been discussing SOBI so far
* Looked at how to choose a set of lags $\{k\}$ for SOBI

---



# Research Meeting Notes

## Pre-June

### GUMP-2s

$$
\begin{aligned}
    &\underline{\textbf{Model: GUMP-2} }
    \\ \quad \\
    X(t) &= c_1(t) Y_1(t) + c_2(t) Y_2(t) 
    \\ \quad \\
    S_X(t,f) &= |c_1(t)|^2 S_{Y_1}(f) + |c_2(t)^2| S_{Y_2}(f) \\
    & \quad\quad {\scriptsize \Big[+ 2c_1(t)c_2(t)S_{Y_1,Y_2}(f) \text{ }\text{ (if }Y_i\text{ correlate)}\Big]}
    \\ \quad \\
    &= S_{X_1}(t,f) + S_{X_2}(t,f) \\ 
\end{aligned}
$$

**What are the least squares estimates of $g$ and $S_Y$? Compute the $N\times N_f$ TFS $S_X$**

* what matrix of the form $gS_Y$ (UMP form) is closest to $S_X$ in frobenius norm? (componentwise sum of squares, least squares estimate)
* For UMP, we get these from the left/right eigenvectors Azadeh derives using SVD: $\hat g_1$ and $\hat S_{Y,1}$
* **SKYE:** do this for GUMP $-$ again, this will probably be singular vectors from the SVD of $\hat S_X$

**Extending UMP decomposition to GUMP-2 decomposition**

* Number of unknowns: $(2N+2M)$
* Partition $\hat S_X$ into first and last $N_f/2$ columns (2 block matrices) $\to 2N$ equations
* Do the same for the rows $\to 2N_f$ equations

*you could make $N_f$ partitions but that wouldn't be smoothing. The number of partitions is essentially a smoothing parameter. Maybe $k$ partitions for GUMP-$k$ is good, then.*

> **Simulation Homework:** Generate GUMP-2s and try to estimate g's

**Say we want to estimate $g(t_0):$**\
**localizing estimation of the vector $g$** *(UMP-only version)*

> *Go to the GUMPs and work out these explicit equations.*

1. Say we do the UMP method: simple rowsums. This is our initial estimate of $g$. 
2. Do the same for S_Y
3. Consider what happens at t_0 across all frequencies. 
    - is it low? high? 
    - Do we expect a better estimate at certain freqs? 
    - We can choose a subset of frequencies whose estimates are more stable.

So this is a 2 stage estimation procedure. Hit $\hat S_X$ with default smoothing, then try to improve. The initial smoothing pulls out g and y, which were unavailable from the $S_X$ to begin with.

**Non-linear systems of equations $\to$ numerical methods:** solve it as an iterative process. 

> *Go to the GUMPs and work out these explicit equations.*

1. Solve for $S_Y$'s first. Then we can set up a linear system of equations.
2. Solve system using QR decomposition (?)
3. Put these back into original
4. Now you have estimates of g
5. I think now you smooth it with the new estimates, getting you better estimates of S
6. Iterate (until when?)
    
    

---

### BSS

**GUMPs: we have 2 $g$ vectors, and 2 $S_Y$ vectors.**

* Partition $S_X$ into 2 vertical block matrices. rowsum first, get N length vec.
* This estimates a linear combo of the g's. Do for second matrix.

$$
\begin{aligned}
     X(t) &=     \\
     &=
\end{aligned}
$$

* 2 vectors = 2x2 mixing matrix times unknown modulating functions
* The modulating functions are like independent source vectors.
* The rowsums [of the output? surely.] are observed time series
* Uniformly modulated sources $\to$ GUMP response (observed)

**Differences between BSS and GUMP decomposition**

* Our proposed system of equations (for GUMPs) don't use evolutionary spectral estimates or anything, but BSS does.
* For GUMP decomp, the $g$'s are deterministic. For BSS, they're treated as random variable.
* In BSS: each observed series is a partial rowsum from the TFS of $X$. That means we'll have to compute the evolutionary cross spectrum.

**Question: in BSS: do we need to observe at least as many series as there are sources?**

---

**BSS version: $S_1$ and $S_2$ are (assuming uncorrelated) like the sources.**
* g_1 and g_2 could also be considered sources tho if you flip it
* (N choose 2) different combos of rows. This would give (N choose 2) estimates of the S_Y's, and we can take some function (average)
* EPS of the sources should have zeroes on the off diagonal since they're uncorrelated.
* So RHS of model is diagonal, want to diagonalize LHS. diagonal matrices are proportional to each other. Do this at each time, that's the algorithm really

> **CODING HOMEWORK:** code bss.
>
    - that means creating STFD matrices.
    - Consider using packages. really.
    - Search BSS in R: JADE, BSS asymp
    - glen wants to know if these will work to estimate $g/S$
    
**BSS: p time series, N obs.**
* mixing matrix p x p; ith diagonal entry is EPS of ith time series at (t,f)
* compute evol spec: each (t,f) has a cross spectrum of the x_i's
* off diagonal entries: sum over the evol eigenspectra: ith time series and jth time series: dot product is estimate of evol cross spec

---

## June

**Some current tasks suggested in meetings**

* add signals to GUMP or UMP to try to them pick up
    - even try AR(2) stationary to compare to MTM
* think about distributional properties of estimators (smooth)
* consider the last few windows for extrapolation
* combine different blockwidths $B$ in various ways

**Decomposition Limitations**

* Any method can only work up to proportionality. The model $x = cY$ doesn't identify $g$ and $S$ the way $A = bc$ doesn't identify $b$ or $c$.
* Decomposition does give us an estimate of total power.
* We can adjust: normalize power such that it's equal to the average power.
* The idea is to get it to c = 1 I guess

#### BSS
* went over BSS framework from Senay
* Consider reviewing Kayhan EP paper
* We got a bit tangled up: what kind of object is the cross spectral power? A vector? Matrix? Before section 3. I think in eq 30 we realize they're time-dependent, freq-dependent diagonal matrices :)
* **CODING:** can I recreate figures 3 and 4? These suggest the estimators are incredibly accurate under simulated conditions, even with noise
* Note that figures uhh 5 and 6 I think? are all messed up. She seems to have added the original source 1 signal to sources 2 and 3, only in the plots (part a)
* reproducing Spatial TFS: take HRS for each time series, get those A hats: rather than squared mag, take $\hat A_x \hat A_y^*$ in eq 11 of Senay
* IDEA: time-varying mixing matrices
    - how can you tell if the source is changing or the mixing is matrix?
    - actually, this might be superfluous. Because any changes in mixing could be attributed to changes in the series themselves, since they're nonstat
    - BUT: what if instead of JD-criterion, what if we just had a tim/freq dependent mix? Or C matrix in senay?

---

## Task breakdown

~TASK 0: resurrect the code files, debug and clean up for ease of use.~

Glen recommends thinking about BSS WHILE I'm working on GUMPs

### GUMP-2
1. Singular value decomposition of GUMP-2 $S_X$.
    - check if this is actually the least squares (even for UMPs, Glen did this informally)
    - would this be the first two L/R eigenvectors? Glen has no reason to believe this. In fact, there would have to be orthogonality between the g's. This is in favour of our method
3. Generalize our decomposition method to GUMP-2:
    - see previous notes for guidance
    - explore the iterative techniques previously outlined (numerical methods)
4. Formally think through window details: blockwidths and tweaks to extrapolation.

**Code:** 

* Check if our smoothing procedure affects our ability to pick up signals.
    - to start: try this for a stationary AR(2) and compare to MTM, Harmonic F, etc. 
* ~Update *The Function* to cover GUMP-2s and additional signals.~
* Generate GUMP-2s
* Generate GUMP-2s with added signal(s).
* Code our partitioning and iterative procedures, try to estimate $g$ and $S_Y$ terms.
* Try normalizing the total power so that it reflects the average power of $S_X$ (see notes)


### BSS
First, go through BSS section of readme to clarify meeting notes and goals

1. Review BSS framework
2. Familiarize myself with how this framework relates to (G)UMPs.
3. Find a way to code the algorithm in R, ideally using established packages














