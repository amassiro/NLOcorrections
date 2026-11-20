MoCaNLO
====

MoCaNLO: Monte Carlo integrator for LO and NLO (EW and QCD) calculations, full off-shell matrix elements
from Recola, Catani-Seymour subtraction, polarised bosons in the double-pole approximation (DPA).

Links:

    https://mocanlo.gitlab.io                          (code and documentation, version 1.0.0)
    User's guide: MoCaNLO 1.0.0, A. Denner, D. Lombardi, S. Lopez Portillo Chavez, M. Pellen, G. Pelliccioli (COMETA-2026-03, FR-PHENO-26-04)
    https://recola.gitlab.io                           (Recola, one-loop amplitudes)
    https://collier.hepforge.org                       (Collier, loop integrals)

ssWW references:

    arXiv:1611.02951   Biedermann, Denner, Pellen: large EW corrections to VBS
    arXiv:1708.00268   Biedermann, Denner, Pellen: complete NLO corrections to W+W+ scattering
    arXiv:2409.03620   Denner, Haitz, Pelliccioli: NLO EW and QCD corrections to polarised ssWW (setup to emulate, future)


Installation
====

Where:

    /gwpool/users/amassiro/Claude/MOCANLO     (hercules + condor nodes + gpu4hep01, shared)
    /home/amassiro/Claude/mocanlo             (pcamassiro)

To install:

    MoCaNLO 1.0.0 + Recola 1.5.1 + Collier 1.2.10, LHAPDF and compilers from LCG_105 (x86_64-centos7-gcc12-opt)
    source /gwpool/users/amassiro/Claude/MOCANLO/setup.sh

NB: the LCG_105 setup leaves IFS = newline only, restore it after sourcing:

    IFS=$' \t\n'

NB: hercules is a shared machine: only short tests, heavy runs on condor (static 1-core slots, ~2.5 GB, request_cpus = 1).


Process and common setup
====

    pp -> e+ ve mu+ vm jj  and  e- ve~ mu- vm~ jj, purely EW at LO, O(alpha^6), sqrt(s) = 13.6 TeV
    full off-shell, no top contributions, photon-induced channels dropped
    PDF NNPDF3.1 NNLO, LHAPDF 325300
    fixed scale muR = muF = MW = 80.419 GeV, 7-point variation in every run
    validated cards: mocanlo-1.0.0/validated_processes/vbs/ssww/{pp_ssww_ew_ew, pp_ssww_ew_qcd}[_minus_minus]

Cuts (loose, emulate the MadGraph generation cuts):

    jets: pT > 10 GeV, |y| < 5, m_jj > 100 GeV (two pT-leading tagging jets)
    charged leptons: |y| < 3, no pT cut, Delta R(j,l), Delta R(l,l) > 0.01, no MET cut

Jet recombination (anti-kt, Delta R in rapidity, four-momenta summed):

    LO (old)          R(j,j) = 0.01 (= MG drjj), R(j,gamma) = 0.4
    NLO (old)         R(j,j) = R(j,gamma) = 0.05
    NLO and pol (new) R(j,j) = R(j,gamma) = 0.4
    lepton-photon dressing: R = 0.1

Histograms: jets pT, y, eta, m_jj, Delta y_jj, Delta eta_jj, Delta phi_jj; leptons pT, y, eta, m_emu, pT_emu,
Delta y, Delta eta, Delta phi; MET, Zeppenfeld z_e, z_mu; m_lvlv = m(e nu mu nu) (0-2000 GeV, 50 GeV bins).


NLO setups
====

    ssww_nlo_13p6/{wpwp,wmwm}_ew     NLO EW, O(alpha^7)
    ssww_nlo_13p6/{wpwp,wmwm}_qcd    NLO QCD, O(alpha_s alpha^6), + LO interference O(alpha_s alpha^5) (born "_int" runs)
    run types: born, real, virt, idip (integrated dipoles); one run per subprocess and contribution
    runs: W+W+ 48 (EW) + 80 (QCD), W-W- 40 (EW) + 74 (QCD)

NLO QCD + NLO EW = LO + delta_EW + delta_QCD (additive, bin by bin); the mixed O(alpha_s alpha^7) is not included.
Not included: QCD-induced background O(alpha_s^2 alpha^4) and its corrections (cards pp_ssww_qcd_ew, pp_ssww_qcd_qcd exist).

Results with R = 0.05 (3 seeds, MC stat only):

    W+W+: LO = 5.174 fb, NLO QCD +10.9 +- 0.7%, NLO EW -11.5 +- 1.1%, sum -0.6 +- 1.2%, INT = 0.708 fb
    W-W-: LO = 1.936 fb, NLO QCD +19.4 +- 0.5%, NLO EW  -7.7 +- 0.7%, sum +11.7 +- 0.9%, INT = 0.248 fb

R = 0.4 rerun:

    ssww_nlo_13p6_R04/    setupR04.sh, runR04Job.sh, runR04.sub (121 condor jobs x 2 runs, time_wall 3h20 per run)


Polarised setups
====

    DPA on both W, on_shell_projection with preserve_system = poldef_system = cms_res (WW CM frame, as arXiv:2409.03620),
    fudge_factor_regularisation (fudge factor 1)
    states: LL, TT, LT, TL (W(e) W(mu)) and UU (unpolarised DPA); interference = UU - (LL + TT + LT + TL)
    NLO photon reals: osp_type 0 (production), 1, 2 (decay of W1, W2)

NB: the QCD-EW interference photon reals ("_int") have no decay dipoles in MoCaNLO ("No dipole found"):
run with osp_type 0 only (FIXME, expected < 0.1% of sigma).

    ssww_pol_13p6/makePolCards.py       LO cards  (wpwp/, wmwm/: old R; lo_R04/: R = 0.4)
    ssww_pol_13p6/makePolNLOCards.py    NLO cards (nlo/: R = 0.05, partial; nlo_R04/: R = 0.4), 1430 runs in total
    ssww_pol_13p6/runPolNLOJob.sh, runPolNLO_R04.sub   (condor, 40 jobs x 1 core)
    ssww_pol_13p6/runPolLocal.sh                       (pcamassiro, NP in parallel)
    ssww_pol_13p6/makePolPlots.sh, sigmaPol.py         (sums, fractions, plots)

LO results, old R (fractions of UU):

    W+W+: UU = 5.140 fb (UU / off-shell = 0.992), LL 9.4%, TT 59.5%, LT+TL 31.2%, interference -0.1 +- 0.2%
    W-W-: UU = 1.925 fb (UU / off-shell = 0.994), LL 9.9%, TT 58.5%, LT+TL 30.5%, interference +1.1 +- 0.1%


Plots
====

    /home/amassiro/Claude/plots_ssww: makeAll.sh (sumHistos -> txt2root -> drawAll.sh), draw.cxx, txt2root.cxx
