# Research Meeting Notes

---

## Pre-June

**GUMPs: 2 g vecs, 2 S vecs.**\
**partition into 2 vertical matrices. rowsum first, get N length vec.**\
**This estimates a linear combo of the g's. Do for second matrix.**

* BSS: above written: 2vecs = 2x2 mixing * unknown modulating funcs
    - so the mod funcs are like indepsource vecs
    - the rowsums are observed time series
* in BSS... need to observe at least as many series as there are sources
    - is this true? It might be too hard though
* you could make Nf partitions but that wouldn't be smoothing. The number of partitions is essentially a smoothing parameter. 
    - Maybe k partitions for gump-k is good then
* techniques for bSS might apply to gumps!
* note that a system of equations doesn't use evolutionary spectral estimates or anything, but bss does
    - but it's not like BSS in that the g's are deterministic, not rvs
    - each obs series is a partial rowsum from tfs x. that means computing evolutionary cross spectrum.

**what is least squares est of g and SY? compute NxNf TFS (S_x);**\
**what is the matrix of the form gSY (ump format) which is closest to S_x in frobenius norm?**\
**(componentwise sum of squares, or least squares estimate)**

* For ump, it's the left/right eigenvectors Azadeh
* skye skye skye, do this for GUMP
* probably again singular vectors of SVD of TFS

**Say we want to estimate g(t_0)**\
**localize estimation of the vector g (UMP-only version)**

1. say we do the UMP method - simple rowsums. this is your initial est of g. Do the same for S_Y
2. consider what happens at t_0 across all frequencies. is it low? high? Do we expect a better estimate at certain freqs? We can choose a subset of frequencies whose estimates are more stable.
3. so this is a 2 stage est procedure. Hit with default smoothing, then try to improve. The initial smoothing pulls out g and y, which were unavailable from the S_X

**Go to the GUMPs and work out these explicit equations.**

* non-linear system of eqs --> num method
* solve it as an iterative process: 
    - solve SY's first. Then you can set up a linear sys of eqs.
    - solve using Qr decomp
    - put these back into original
    - now you have ests of g
    - use to get better sums of S
    - go in a circle (iterative)

* uniformly modulated sources --> GUMP response (observed)
* 2-Gump: i,jth entry = time i, freq j

**GUmp-2 model: $\alpha_1 c_1 Y_1$ + $\alpha_2 c_2 Y_2$**

* EPS(t,f) = a^2 g S_Y^2 and so forth
* $2N+2M+2$ unknowns (can work these out in your head). ACTUALLY, (2N+2M) since alphas a wlog
* partition into 1st and last M/2 columns (2 block matrices) -> 2N equations
* Do the same for the rows -> 2M eqs

**Simulation Homework**

* assume alphas are known (custom parameters) then generate GUMP-2s and try to estimate g's
* actually the alphas are without loss of generality since they're just factors. that's the same purpose as g

**BSS version: $S_1$ and $S_2$ are (assuming uncorrelated) like the sources.**

* g_1 and g_2 could also be considered sources tho if you flip it
* (N choose 2) different comos of rows. This would give (N choose 2) estimates of the S_Y's, and we can take some function (average)
* EPS of the sources should have zeroes on the off diagonal since they're uncorrelated.
* So RHS of model is diagonal, want to diagonalize LHS. diagonal matrices are proportional to each other. Do this at each time, that's the algorithm really
* **CODING HOMEWORK:** code bss.
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

* add signals to gump or UMP to try to pick up
    - even try AR(2) stationary to compare to MTM
* think about distributional properties of estimators (smooth)
* consider the last few windows for extrapolation
* combine different Bs in various ways

**Decomposition Limitations**

* Any method can only work up to proportionality. The model x = cY. It doesn't identify g and S the way A = bc doesn't identify
* does give us estimate of total power.
* We can adjust - normalize so that power is average power.
* Idea is to get it to c = 1 I guess



















### June 20

