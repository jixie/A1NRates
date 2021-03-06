Program to calculate rate using XSModel.
XSModel return DiffXS in ub/MeV-Sr for inelas or ub for elas.
 
To be improved:
1) HMS and SHMS acceptance.
   One need to run single-arm-simulation program to get the angle acceptance. 
   This angle acceptance should have a z dependence.  I am not sure if the
   single-arm-simulation program has already taken z dependence into account.
   If not, one need to develop a transportation packing for single-arm-simulation
   program first.

2) Make A1NRates.cc as a class such that it can be used in root cint.

First release version v1.0.0.

1. Hard-coded HMS and SHMS collimator size and angle acceptance, need to be improved.
2. Hard-coded target material, it will be better to separate them.


20190708
1. Changed GetInteXS() to bin in CosTheta. Optimized HMS and SHMS collimator size and angle
   acceptance to match method 2.
2. Changed GetInteXS():
*//old version is binned in Theta-Phi-P, new version is binned in CosTheta-Phi-P
*//get integrated xs for given element at given vertex z,
*//applying x, q2 and w cuts if their upper limits are larger than zero
*//ElasOnly=-1: pure inelastic for full acceptance
*//ElasOnly=0:  inelastic + elastic for full acceptance
*//ElasOnly=1:  pure elastic for full acceptance
*//ElasOnly=-30: pure inelastic for 2-SC-Bar acceptance
*//ElasOnly=30: inelastic + elastic for 2-SC-Bar acceptance
*//ElasOnly=31: pure elastic for 2-SC-Bar acceptance
*//double GetInteXS(double pBeamE, double pAngle, double pMomentum, double Z, int N, double VZ, string Name, int ElasOnly=0,
*//                 double pXmin=-1.0, double pXmax=-1.0, double pQ2min=-1.0, double pQ2max=-1.0,double pWmin=-1.0, double pWmax=-1.0)
*
*   A) W cut is no longer identified by ElasOnly flag.  User will provide Wmin and Wmax. By default
*   Wmin=Wmax=-1.0.  W cut will be applied only if Wmax>=0;
*   B) Added more ElasOnly flags. If ElasOnly<0, will do inelastic xs only. Use ElasOnly=-30,30,31 for 2-SC-Bar acceptance calculation.
*   Use ElasOnly=-1,0,1 for full acceptance calculation.
*   C) Final cuts: no collimation cut on honrizontal angle (Phi_tg), but apply vertical angle (Theta_tg) cut to match the result of method 2.
*   D) Final bin width: dCosth=0.0005, dPhi=0.1, dP=0.002

3. Changed GetRate():
*   A) Only 40cm 3He target will do X bin calculation.
*   B) If (ElasOnly < 0), only do inelastic calculation for H2, N2 and 3He target.
*   C) C12 and pressure curve calculation will be done only for elastic P0.
*   D) all 40cm long target will do calculation in 20 VZ bins.
*   E) Update Xbj bin to match Xiaochao's binning.

4. Changed Main.cc. If no argument is used, it will call A1NRates() to calculate rates for the whole A1N kinematic points.
   If 5 arguments are given, it will call GetRate(XXX) to calculate rates for the given point.
*   
* Usage: ./a1nrates <BeamCurrent_uA> <Beam_GeV> <DetectorAngle_deg> <DetectorMomentum_GeV> <DetectorName=HMS|SHMS> [ElasOnly=0]
*        All energies are in GeV unit. All angles are in degree unit.
*        ElasOnly=-1: pure inelastic for full acceptance.
*        ElasOnly=0:  inelastic + elastic for full acceptance.
*        ElasOnly=1:  pure elastic for full acceptance.
*        ElasOnly=2:  inelastic + elastic for full acceptance, with cut of 1.10<W<1.35.
*        ElasOnly=4:  inelastic + elastic for full acceptance. with cut of 2.00<W<100.0
*        ElasOnly=-30: pure inelastic for 2-SC-Bar acceptance.
*        ElasOnly=30: inelastic + elastic for 2-SC-Bar acceptance.
*        ElasOnly=31: pure elastic for 2-SC-Bar acceptance.
        
5. The best acceptance files have been placed here: ifarm1401:/work/halla/g2p/disk1/jixie/A1N/acceptance/

6. Added GetBeamPara() to calculate suggested beam current and pre-scale factor.

7. Added D2NRates() to calculate rates for d2n. main() was changed to adapt this change. New usage is as the following.

 Calcualte rates for A1N or D2N. There are 2 modes to run this program:
*
* Usage 1: ./a1nrates <task=-1|-2> 
*          This will calculate rates for all kinematic points for A1N(task=-1) or d2n(task=-2)
*
* Usage 2: ./a1nrates <BeamCurrent_uA> <Beam_GeV> <DetectorAngle_deg> <DetectorMomentum_GeV> <DetectorName=HMS|SHMS> [ElasOnly=0]
*        All energies are in GeV unit. All angles are in degree unit.
*        ElasOnly=-1: pure inelastic for full acceptance.
*        ElasOnly=0:  inelastic + elastic for full acceptance.
*        ElasOnly=1:  pure elastic for full acceptance.
*        ElasOnly=2:  inelastic + elastic for full acceptance, with cut of 1.10<W<1.35.
*        ElasOnly=4:  inelastic + elastic for full acceptance. with cut of 2.00<W<100.0
*        ElasOnly=-30: pure inelastic for 2-SC-Bar acceptance.
*        ElasOnly=30: inelastic + elastic for 2-SC-Bar acceptance.
*        ElasOnly=31: pure elastic for 2-SC-Bar acceptance.

8.  Added GetA1NTaTb() to calculate Ta and Tb for radiation correction.

9.  Fixed bug in GetA1NTaTb(). Changed C12 foil target thickness from 100 mil to 10 mil.

10. Added A1NOptics(), GetOpticsRate() and GetOpticsBeamPara() only for optics.

11. Added He3FormFactorRates() to calculate rates for He3 form factor experiment.

12. Updated GetOpticsBeamPara() and GetBeamPara() so it will provide 2-arm run plan assuming HMS get priority.

13. Added global variables to control if or not to calculate C12 rates or Pressure curve. 
* static bool  gSkipC12 = false;
* static bool  gSkipPresureCurve = false;

14. For 12AMG helium3 target, use very fine bin to integrated over Xbj bin so we can have reliable rate in Xbj bins.

