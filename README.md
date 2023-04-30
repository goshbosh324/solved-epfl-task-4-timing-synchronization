Download Link: https://assignmentchef.com/product/solved-epfl-task-4-timing-synchronization
<br>
The timing problem is easily understood: The receiver samples the incoming signal with the known sampling rate 1<em>/T<sub>s</sub></em>, but how does it know exactly <em>when </em>to sample? It does not know this, it can only sample the signal with a random time offset, and it is the task of the time synchronization unit to estimate this time offset and correct it.

<h1>4.1     Effect of a Timing Offset</h1>

The effect of the timing offset can be modelled by including the filter <em>δ</em>(<em>t</em>−<em>εT</em>) into the effective channel. The parameter <em>ε </em>denotes the time offset, normalized by the symbol duration <em>T</em>. The matched filter output then becomes

<em>.                 </em>(4.1)

Take a look at Figure 4.1, where several RC pulses are plotted. Imagine that we want to detect the data symbol <em>a</em>[0]. If we sample the signal exactly at <em>t </em>= 0, all pulses except of the middle one are zero, and the received sample is equal to the data symbol plus noise. Now assume that we have a timing error of, say, <em>ε </em>= 0<em>.</em>1, which means that the signal arrives a bit too late, and hence our receiver samples the signal at <em>t </em>= −0<em>.</em>1<em>T</em>. At this time, however, the center pulse has

Figure 4.1: Raised cosine pulses with <em>α </em>= 0<em>.</em>22

Figure 4.2: Time synchronization unit

not yet reached its maximum amplitude; furthermore, the contributions of the other pulses do not vanish, which leads to inter-symbol interference.

In order to attain ISI-free reception, we must attempt to generate symbols

(4.2)

where <em>ε</em>ˆ is an estimate of the timing error. If <em>ε</em>ˆ = <em>ε</em>, then <em>z</em>(<em>nT </em>+ <em>εT</em>ˆ ) = <em>a</em>[<em>n</em>] + <em>w</em><sup>′</sup>(<em>nT </em>+ <em>εT</em>ˆ ) and the data can be optimally recovered.

<h1>4.2 Synchronization via Interpolation</h1>

In principle, there are two possibilities to generate the desired symbols <em>z</em>(<em>nT </em>+ <em>εT</em>ˆ ). The first possibility is to adjust the time instant of A/D conversion. However, this results in an undesired feedback to the analog part of the system. We will therefore use a different method that works completely in the digital domain.

It is well known that a bandlimited signal is completely described by time-discrete samples if 1<em>/T<sub>s </sub></em>≥ 2<em>f</em><sub>max</sub>, where <em>f</em><sub>max </sub>is the highest frequency that is contained in the signal. Due to the oversampling, this condition is fulfilled in our case. It is thus possible to calculate arbitrary intermediate values from the sample sequence by means of an interpolator.

Figure 4.2 shows a block diagram of the time synchronization unit. The timing estimator calculates an estimated timing offset <em>ε</em>ˆ using the received samples <em>z</em>(<em>kT<sub>s</sub></em>). The interpolator then generates the time-delayed sample stream <em>z</em>(<em>kT<sub>s </sub></em>+ <em>εT</em>ˆ ) which is then converted to symbol rate by discarding the <em>L </em>− 1 intermediate samples between two adjacent data symbols.

The interpolator has a strong impact on the implementation complexity. An ideal interpolator would be implemented as an si-filter, and its impulse response would have to be of infinite duration. Of course, from a practical point of view, the interpolator is only allowed to have a finite number of taps. A brief discussion of practical interpolators is given in Appendix 4.6.

<h1>4.3 The Estimation Algorithm</h1>

The remaining question is how to obtain the estimate <em>ε</em>ˆ. For the derivation of the estimator we use the sequence <em>x</em>[<em>k</em>] = <em>x</em>(<em>kT<sub>s</sub></em>) which is defined as the instantaneous signal power

<em>x</em>(<em>kT<sub>s</sub></em>) = |<em>z</em>(<em>kT<sub>s</sub></em>)|<sup>2</sup><em>.                                                               </em>(4.3)

4.3: TheEstimationAlgorithm

Appendix 4.5 shows the following relation: if there is no time offset (<em>ε </em>= 0), then the expectation of <em>x</em>(<em>kT<sub>s</sub></em>) is a real-valued and even signal. Accordingly, its spectrum (i.e., the expectation of <em>X</em>(<em>f</em>)) is then real-valued for all frequencies <em>f</em>. Now, if we introduce a nonzero offset <em>εT </em>in <em>z</em>(<em>kT<sub>s</sub></em>) and, accordingly, in <em>x</em>(<em>kT<sub>s</sub></em>), the original real spectrum is multiplied by <em>e</em><sup>−2<em>πjεTf</em></sup>. Evaluating the spectrum at the symbol rate <em>f </em>= 1<em>/T </em>then results in a complex value whose phase is −2<em>πε</em>. We can thus use

(4.4)

as an estimator of the timing offset (see Appendix 4.5 for details).

The spectral component <em>X</em>(1<em>/T</em>) can be calculated by performing a <em>discrete fourier transform </em>(DFT). In general, the spectrum <em>X</em>(<em>f</em>) is given as

<em>,                                        </em>(4.5)

where we have assumed that the signal starts at <em>t </em>= 0. The spectral component at <em>f </em>= 1<em>/T </em>is

thus

(4.6)

Due to the oversampling factor of <em>T/T<sub>s </sub></em>= 4, the implementation of the Fourier transform becomes very simple, as the exponential term only takes on the values {1<em>,</em>−<em>j,</em>−1<em>,j</em>}.

Of course, Equation (4.6) cannot be implemented in a real system, because we cannot calculate an infinite sum. We can, however, get an estimate <em>ε</em>ˆ[<em>n</em>] that is updated every symbol duration by taking all available samples into account:

<em> .                                  </em>(4.7)

The cumulative sum acts as an averager: During the reception, more samples become available, and the estimate becomes more and more reliable.

It is easily seen that the algorithm (4.7) only works if the true parameter <em>ε </em>is constant during the whole reception, which is the case in this lab. In practice, however, the timing offset will change over time, and the estimation accuracy would be severely degraded if we took all available samples—even old ones that were caused by a different <em>ε </em>than the current—into account.

An important measure here is the <em>coherence time</em>, which is the duration over which a parameter can assumed to be approximately constant. Let the coherence time for the timing offset be denoted as <em>T</em><sub>c;<em>ε </em></sub>= <em>N</em><sub>c;<em>ε</em></sub><em>T</em>. Then the estimation algorithm can be implemented as a sliding sum over the 4<em>N</em><sub>c;<em>ε </em></sub>most recently received samples:

<em>.                            </em>(4.8)

<h1>4.4 Your Tasks</h1>

<ol>

 <li>Implement the timing synchronization unit and integrate it into the system provided on the Moodle (timingsync.m). At first, write and use a linear interpolator to decode the image (task4.mat).</li>

 <li>Afterwards, adapt your system to do BER measurements. We provide a symbol sequence(pn sequence.mat) and the corresponding bitstream (ber pn seq.mat) to do this.</li>

 <li>Plot <em>ε</em>ˆ[<em>n</em>] over <em>n</em>. You will notice that the first estimates are very bad, because the averaging is over too few symbols. Can you imagine a way to improve these first estimates? (Think about the frame synchronizer!)</li>

 <li>Now replace the interpolator by a cubic one (see Appendix 4.6). How big is the performance gain?</li>

</ol>

<h1>4.5 Appendix</h1>

In this section, we show that (4.4) is an <em>asymptotically unbiased </em>estimator of the timing offset <em>ε</em>. (An estimator is asymptotically unbiased if it becomes unbiased as the number of observations goes to infinity.)

<h2>4.5.1 Signal Model</h2>

We assume <em>M</em>-PSK signalling, which means that the amplitude of the data symbols <em>a</em>[<em>n</em>] is constant. Without loss of generality, set<sup>∗ </sup>|<em>a</em>[<em>n</em>]| = 1. The symbols are assumed to be uncorrelated, i.e. <em>E</em>{<em>a</em>[<em>m</em>]<em>a </em>[<em>n</em>]} = <em>δ</em>(<em>m </em>− <em>n</em>). The received signal after matched filtering is given as

<em>,                                     </em>(4.9)

where <em>g</em><sub>RC</sub>(<em>t</em>) is the combined impulse response of the pulse shaping filter and the matched filter, and is therefore real-valued and even. In our lab, <em>g</em><sub>RC</sub>(<em>t</em>) is a raised cosine pulse. Furthermore, <em>w</em><sup>′</sup>(<em>t</em>) is the AWGN with variance <em>σ<sub>w</sub></em><sup>2 </sup>after matched filtering.

For the digital realization, the signal is sampled at four times the symbol rate: <em>T/T<sub>s </sub></em>= 4.

<h2>4.5.2 Continuous Time Consideration</h2>

We start with examining the noise-free signal <em>z</em>˜(<em>t</em>) = <em>z</em>(<em>t</em>) − <em>w</em><sup>′</sup>(<em>t</em>). Let

<em>x</em>¯(<em>t</em>) = <em>E</em>{|<em>z</em>˜(<em>t</em>)|<sup>2</sup>}                                                             (4.10)

4.5: Appendix

denote the expected value of the instantaneous signal power. Since the data symbols are uncorrelated, <em>x</em>¯(<em>t</em>) becomes

(4.11)

The function <em>x</em>¯(<em>t</em>) is periodic with period <em>T</em>

(4.12)

with <em>n</em><sup>′ </sup>= <em>n </em>− 1

and can therefore be expanded into a Fourier series

<em>.                                             </em>(4.13)

The Fourier coefficients are given as

(4.14)

The first Fourier coefficient, i.e. the spectral component <em><sup>X</sup></em>¯(1<em>/T</em>), is

substitute <em>u </em>= <em>t </em>− <em>εT</em>

(4.15)

real and even

Thus, we can calculate the timing offset perfectly from the noise-free and time-continuous received signal:

<em>.                                           </em>(4.16)

<h2>4.5.3 Discrete Time Realization</h2>

Now we consider the noisy and sampled signal

<table width="605">

 <tbody>

  <tr>

   <td width="225">and show that</td>

   <td width="243"><em>x</em>[<em>k</em>] = |<em>z</em>(<em>kT<sub>s</sub></em>)|<sup>2</sup></td>

   <td width="137">(4.17)</td>

  </tr>

 </tbody>

</table>

(4.18)

is an asymptotically unbiased estimate of <em>ε</em>.

We start by calculating the expected value of <em>x</em>[<em>k</em>]:

(4.19)

with the noise-free signal <em>x</em>¯(<em>t</em>) that we have examined in the previous section.

Assuming that the signal starts at <em>k </em>= 0, the corresponding spectrum is

<em>.                 </em>(4.20)

Evaluating this for <em>f </em>= 1<em>/T </em>yields

<em>.                                           </em>(4.21)

If we approximate <em>E</em>{<em>X</em>(1<em>/T</em>)} at time <em>t </em>= <em>nT </em>by using the 4<em>N</em><sub>c;<em>ε </em></sub>most recently received samples (the <em>N</em><sub>c;<em>ε </em></sub>most recently received symbols), we get the estimate

<em>.                                    </em>(4.22)

Since <em>x</em>¯(<em>t</em>) is bandlimited to ±2<em>/T </em>we can calculate this from <em>x</em>¯(<em>t</em>), rather than from the discrete <em>x</em>¯(<em>kT<sub>s</sub></em>):

(4.23)

with <em>c</em><sub>1 </sub>calculated in (4.15).

4.6: Appendix

We can now calculate the expected value of <em>ε</em>ˆ:

(4.24)

Due to the “approximately equal” sign, the estimator is strictly speaking biased. However, if the number of samples is very large, the variance of <em><sup>X</sup></em>ˆ(1<em>/T</em>) becomes small, and the expectation and argument operations can be exchanged. Our estimator is therefore asymptotically unbiased.

<h1>4.6     Appendix</h1>

<h2>4.6.1    Perfect Interpolator</h2>

Let <em>f</em>(<em>x</em>) be a (possibly complex-valued) function of one real-valued parameter. Assume that we only know <em>f</em>(<em>x</em>) for <em>x </em>∈ Z, i.e. a series of samples. If <em>f </em>is bandlimited with a maximum frequency of 1<em>/</em>2, then the function is completely described by the samples. The problem of finding <em>f</em>(<em>x</em>) for <em>x /</em>∈ Z is called interpolation.

The ideal interpolator is a lowpass filter, i.e. the function can be calculated from its samples as

<em>.                                   </em>(4.25)

However, this interpolator is not realizable because it has infinitely many taps.

<h2>4.6.2    Linear Interpolator</h2>

The simplest possible interpolator is the linear one. Assume without loss of generality that we are interested in the intermediate value <em>f</em>(<em>x</em>) with <em>x </em>∈ (0;1). The linear interpolator then calculates <em>f</em>(<em>x</em>) using the two closest samples <em>f</em>(0) and <em>f</em>(1) according to

<em>f</em>(<em>x</em>) = (1 − <em>x</em>)<em>f</em>(0) + <em>xf</em>(1)<em>.                                         </em>(4.26)

The linear interpolator can be implemented very efficiently, but its accuracy can be problematic. Consider the situation illustrated in Figure 4.3. Given are the function and

the samples <em>f</em>(0) = <em>f</em>(1) = 1<em>/</em>√2. Now, for all <em>x </em>∈ (0;1), the linear interpolator returns

<em>f</em>(<em>x</em>) = 1<em>/</em>p(2), which is obviously a very bad approximation of the true value if <em>x </em>is around

0.5.

On the other hand, if we wanted to estimate a value with <em>x </em>∈ (−1;0) using the samples <em>f</em>(−1) and <em>f</em>(0), the linear estimation would be quite good.

We see that the linear interpolator yields especially bad results if the function has a local maximum or minimum between the two considered samples.

Figure 4.3: Linear interpolator for

<h2>4.6.3 Cubic Interpolator</h2>

The interpolation can be improved by taking one additional sample on each side into account, which leads us to the cubic interpolator. Again we assume that we want to estimate <em>f</em>(<em>x</em>) with <em>x </em>∈ (0;1), this time using the samples <em>f</em>(−1), <em>f</em>(0), <em>f</em>(1) and <em>f</em>(2). The idea is to approximate <em>f</em>(<em>x</em>) with a cubic polynomial <em>f</em>˜(<em>x</em>) and then evaluate it at the desired point.

<table width="605">

 <tbody>

  <tr>

   <td width="413">In general,</td>

   <td width="192"> </td>

  </tr>

  <tr>

   <td width="413"><em>f</em>˜(<em>x</em>) = <em>ax</em><sup>3 </sup>+ <em>bx</em><sup>2 </sup>+ <em>cx </em>+ <em>d,</em>where the coefficients <em>a</em>, <em>b</em>, <em>c</em>, <em>d </em>must satisfy</td>

   <td width="192">(4.27)</td>

  </tr>

 </tbody>

</table>

<em> .                                       </em>(4.28)

With a matrix inversion we get the result

<em> .                                 </em>(4.29)