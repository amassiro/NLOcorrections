MoCaNLO
====

MoCaNLO: Monte Carlo integrator for LO and NLO (EW and QCD) calculations, full off-shell matrix elements
(Recola), Catani-Seymour subtraction, polarised bosons in the double-pole approximation (DPA).

Links:

    https://mocanlo.gitlab.io                          (code and documentation, version 1.0.0)
    User's guide: MoCaNLO 1.0.0, A. Denner, D. Lombardi, S. Lopez Portillo Chavez, M. Pellen, G. Pelliccioli (COMETA-2026-03, FR-PHENO-26-04)
    https://recola.gitlab.io                           (Recola, one-loop amplitudes)
    https://collier.hepforge.org                       (Collier, loop integrals)

ssWW references:

    arXiv:1611.02951   Biedermann, Denner, Pellen: large EW corrections to VBS
    arXiv:1708.00268   Biedermann, Denner, Pellen: complete NLO corrections to W+W+ scattering
    arXiv:2409.03620   Denner, Haitz, Pelliccioli: NLO EW and QCD corrections to polarised ssWW


Process and setup
====

    pp -> e+ ve mu+ vm jj  and  e- ve~ mu- vm~ jj, purely EW at LO, O(alpha^6), sqrt(s) = 13.6 TeV
    full off-shell, no top-quark contributions, photon-induced channels not included
    PDF NNPDF3.1 NNLO (LHAPDF 325300)
    fixed scale muR = muF = MW = 80.419 GeV, 7-point scale variation

Cuts (loose, emulating the MadGraph generation cuts):

    jets: pT > 10 GeV, |y| < 5, m_jj > 100 GeV (m_jj of the two pT-leading tagging jets)
    charged leptons: |y| < 3, no pT cut
    Delta R(j,l), Delta R(l,l) > 0.01, no MET cut

Jet recombination: anti-kt, Delta R in rapidity, four-momenta summed (E scheme).

    R(j,j) = R(j,gamma) = 0.4        (first NLO version: R = 0.05; LO: R(j,j) = 0.01)
    lepton-photon dressing: R = 0.1

NB: with a small R (0.05) quark and gluon are almost never recombined in the real QCD emission: the gluon
can become one of the two tagging jets, m_jj and the jet observables depend strongly on R.

Observables: jets pT, y, eta, m_jj, Delta y_jj, Delta eta_jj, Delta phi_jj; leptons pT, y, eta, m_emu, pT_emu,
Delta y_emu, Delta eta_emu, Delta phi_emu; MET; Zeppenfeld z_e, z_mu; m_lvlv = m(e nu mu nu).


NLO
====

    LO              O(alpha^6)
    NLO EW          O(alpha^7)
    NLO QCD         O(alpha_s alpha^6)
    LO interference O(alpha_s alpha^5), kept separate
    contributions: born, virtual, real, integrated dipoles

NLO QCD + NLO EW = LO + delta_EW + delta_QCD (additive, bin by bin). The mixed O(alpha_s alpha^7) is not included
(the multiplicative combination can be used to estimate its size).
Not included: QCD-induced background O(alpha_s^2 alpha^4) and its corrections O(alpha_s^2 alpha^5), O(alpha_s^3 alpha^4).

Results (MC stat. uncertainties only), R = 0.4:

    W+W+: LO = 5.182 fb, NLO QCD +17.3 +- 0.6%, NLO EW -10.4 +- 0.6%, QCD+EW (sum)  +6.9 +- 0.9%, interference = 0.709 fb
    W-W-: LO = 1.938 fb, NLO QCD +32.7 +- 1.0%, NLO EW  -9.7 +- 0.4%, QCD+EW (sum) +23.0 +- 1.1%, interference = 0.249 fb

R = 0.05:

    W+W+: LO = 5.174 fb, NLO QCD +10.9 +- 0.7%, NLO EW -11.5 +- 1.1%, QCD+EW (sum)  -0.6 +- 1.2%, interference = 0.708 fb
    W-W-: LO = 1.936 fb, NLO QCD +24.1 +- 0.5%, NLO EW  -8.9 +- 0.7%, QCD+EW (sum) +15.2 +- 0.9%, interference = 0.248 fb

NB: runs with different seeds combined with 1/sigma^2 weights. The real-emission weights are heavy-tailed:
with a plain mean W+W+ (R = 0.4) gives NLO EW = -0.63 +- 0.06 fb and NLO QCD = +0.96 +- 0.04 fb (vs -0.54, +0.89 fb).


Polarisation
====

    DPA for both W bosons, on-shell projection preserving the WW system
    polarisation vectors defined in the WW centre-of-mass frame (as in arXiv:2409.03620)
    states: LL, TT, LT, TL (W(e) W(mu)) and UU (unpolarised DPA)
    interference = UU - (LL + TT + LT + TL)
    NLO: photon real emission with separate on-shell projections for production and for the decay of each W

NB: the QCD-EW interference photon real emission needs the decay projections too: in MoCaNLO 1.0.0 they are
missing (see "MoCaNLO issues" below), the effect is NOT small (e.g. W+W+ uu_int real 2.4 +- 1.0 fb vs 0.014 fb off-shell).

LO results, R(j,j) = 0.01 (fractions of UU):

    W+W+: UU = 5.140 fb (UU / off-shell = 0.992), LL 9.4%, TT 59.5%, LT+TL 31.2%, interference -0.1 +- 0.2%
    W-W-: UU = 1.925 fb (UU / off-shell = 0.994), LL 9.9%, TT 58.5%, LT+TL 30.5%, interference +1.1 +- 0.1%

NLO EW results, R = 0.4 (DPA, delta_EW = (NLO EW - LO) / LO, LO = Born of the NLO EW runs, MC stat. on delta_EW <= 0.4%):

    W+W+: LL 0.484 fb -7.7%, TT 3.057 fb -13.4%, LT+TL 1.606 fb -11.2%, UU 5.145 fb -12.3% (off-shell: 5.182 fb, -10.4%)
    W-W-: LL 0.191 fb -6.6%, TT 1.124 fb -11.7%, LT+TL 0.588 fb  -9.4%, UU 1.927 fb -10.5% (off-shell: 1.938 fb,  -9.7%)
    fractions of UU change by < 1% (absolute) from LO to NLO EW

NB: K = NLO EW / LO differs between the states (LL vs the others incompatible bin by bin, chi2 p < 1e-8):
a single K factor for all polarisations is not adequate.

NLO QCD (polarised, DPA): NOT usable yet. The real QCD runs with a q q' pair that can come from a W
(e.g. u g -> l+ v l+ v d d u~) are 20-30% below the off-shell ones, with rare events of weight ~ the full integral
(1/sigma^2 weighted mean biased low, plain mean dominated by single events). Under study: the fudge-factor
regularisation of the non-projected resonances looks inconsistent in the subtraction terms (mapped process
u u -> l+ v l+ v d d gets a fudge factor on the (d, d) pair). keep_resonance_widths = 1 is not supported for the real runs.


MoCaNLO issues
====

Found with the polarised NLO runs, MoCaNLO 1.0.0, patches in /home/amassiro/Claude/mocanlo/patches:

    fudgefix: on_shell_projections.F90, osp_general_compute_fudge_factor: momenta of the wrong legs summed
              (fudge_indices(k,1) instead of fudge_indices(i,k)), width-range check applied only to the last resonance
    fix2:     subtraction.F90: for a QCD-EW interference (tree_qcd_order /= tree_qcd_order_2) the decay dipoles are
              searched for a gluon (rpdg = 21) also for photon real emission -> no decay dipoles; patched to rpdg = 22
              when the real process has an outgoing photon (fix2 includes fudgefix)

Cards (W-W-): for the photon real emission partonic_process was set equal to the real process (photon included)
-> no decay dipoles, the osp1/osp2 runs stop with "No dipole found" and the decay radiation is unsubtracted
(W-W- delta_EW = +158%). Fixed in the card generation (makePolNLOCards.py -b -n): partonic_process = real process
without the photon, decay projections also for the interference.

NB: 130 runs stop by design ("No dipole found" for osp runs without decay dipoles, integrated dipoles without dipoles).
