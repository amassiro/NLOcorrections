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

Results with R = 0.05 (MC stat. uncertainties only):

    W+W+: LO = 5.174 fb, NLO QCD +10.9 +- 0.7%, NLO EW -11.5 +- 1.1%, QCD+EW (sum) -0.6 +- 1.2%, interference = 0.708 fb
    W-W-: LO = 1.936 fb, NLO QCD +19.4 +- 0.5%, NLO EW  -7.7 +- 0.7%, QCD+EW (sum) +11.7 +- 0.9%, interference = 0.248 fb


Polarisation
====

    DPA for both W bosons, on-shell projection preserving the WW system
    polarisation vectors defined in the WW centre-of-mass frame (as in arXiv:2409.03620)
    states: LL, TT, LT, TL (W(e) W(mu)) and UU (unpolarised DPA)
    interference = UU - (LL + TT + LT + TL)
    NLO: photon real emission with separate on-shell projections for production and for the decay of each W

NB: for the QCD-EW interference photon real emission only the production projection is available
(no decay dipoles), expected effect < 0.1% of sigma.

LO results, R(j,j) = 0.01 (fractions of UU):

    W+W+: UU = 5.140 fb (UU / off-shell = 0.992), LL 9.4%, TT 59.5%, LT+TL 31.2%, interference -0.1 +- 0.2%
    W-W-: UU = 1.925 fb (UU / off-shell = 0.994), LL 9.9%, TT 58.5%, LT+TL 30.5%, interference +1.1 +- 0.1%
