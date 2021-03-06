# Copyright (C) 1995-2019, Rene Brun and Fons Rademakers.
# All rights reserved.
#
# For the licensing terms see $ROOTSYS/LICENSE.
# For the list of contributors see $ROOTSYS/README/CREDITS.

############################################################################
# CMakeLists.txt file for building ROOT rootfit package
# @author Pere Mato, CERN
############################################################################


ROOT_STANDARD_LIBRARY_PACKAGE(RooFit
  HEADERS
    Roo2DKeysPdf.h
    RooArgusBG.h
    RooBCPEffDecay.h
    RooBCPGenDecay.h
    RooBDecay.h
    RooBernstein.h
    RooBifurGauss.h
    RooBlindTools.h
    RooBMixDecay.h
    RooBreitWigner.h
    RooBukinPdf.h
    RooCBShape.h
    RooDSCBShape.h
    RooSDSCBShape.h
    RooCFunction1Binding.h
    RooCFunction2Binding.h
    RooCFunction3Binding.h
    RooCFunction4Binding.h
    RooChebychev.h
    RooChi2MCSModule.h
    RooChiSquarePdf.h
    RooDecay.h
    RooDstD0BG.h
    RooExponential.h
    RooFunctor1DBinding.h
    RooFunctorBinding.h
    RooGamma.h
    RooGaussian.h
    RooGaussModel.h
    RooGExpModel.h
    RooHistConstraint.h
    RooIntegralMorph.h
    RooJeffreysPrior.h
    RooKeysPdf.h
    RooLandau.h
    RooLognormal.h
    RooMathCoreReg.h
    RooMomentMorphFunc.h
    RooMomentMorphFuncND.h
    RooMomentMorph.h
    RooMomentMorphND.h
    RooMultiBinomial.h
    RooNDKeysPdf.h
    RooNonCPEigenDecay.h
    RooNovosibirsk.h
    RooParametricStepFunction.h
    RooParamHistFunc.h
    RooPoisson.h
    RooPolynomial.h
    RooStepFunction.h
    RooTFnBinding.h
    RooTFnPdfBinding.h
    RooTMathReg.h
    RooUnblindCPAsymVar.h
    RooUnblindOffset.h
    RooUnblindPrecision.h
    RooUnblindUniform.h
    RooUniform.h
    RooVoigtian.h
    RooJohnson.h
  SOURCES
    src/Roo2DKeysPdf.cxx
    src/RooArgusBG.cxx
    src/RooBCPEffDecay.cxx
    src/RooBCPGenDecay.cxx
    src/RooBDecay.cxx
    src/RooBernstein.cxx
    src/RooBifurGauss.cxx
    src/RooBlindTools.cxx
    src/RooBMixDecay.cxx
    src/RooBreitWigner.cxx
    src/RooBukinPdf.cxx
    src/RooCBShape.cxx
    src/RooDSCBShape.cxx
    src/RooSDSCBShape.cxx
    src/RooCFunction1Binding.cxx
    src/RooCFunction2Binding.cxx
    src/RooCFunction3Binding.cxx
    src/RooCFunction4Binding.cxx
    src/RooChebychev.cxx
    src/RooChi2MCSModule.cxx
    src/RooChiSquarePdf.cxx
    src/RooDecay.cxx
    src/RooDstD0BG.cxx
    src/RooExponential.cxx
    src/RooFunctor1DBinding.cxx
    src/RooFunctorBinding.cxx
    src/RooGamma.cxx
    src/RooGaussian.cxx
    src/RooGaussModel.cxx
    src/RooGExpModel.cxx
    src/RooHistConstraint.cxx
    src/RooIntegralMorph.cxx
    src/RooJeffreysPrior.cxx
    src/RooKeysPdf.cxx
    src/RooLandau.cxx
    src/RooLognormal.cxx
    src/RooMathCoreReg.cxx
    src/RooMomentMorph.cxx
    src/RooMomentMorphFunc.cxx
    src/RooMomentMorphFuncND.cxx
    src/RooMomentMorphND.cxx
    src/RooMultiBinomial.cxx
    src/RooNDKeysPdf.cxx
    src/RooNonCPEigenDecay.cxx
    src/RooNovosibirsk.cxx
    src/RooParametricStepFunction.cxx
    src/RooParamHistFunc.cxx
    src/RooPoisson.cxx
    src/RooPolynomial.cxx
    src/RooStepFunction.cxx
    src/RooTFnBinding.cxx
    src/RooTFnPdfBinding.cxx
    src/RooTMathReg.cxx
    src/RooUnblindCPAsymVar.cxx
    src/RooUnblindOffset.cxx
    src/RooUnblindPrecision.cxx
    src/RooUnblindUniform.cxx
    src/RooUniform.cxx
    src/RooVoigtian.cxx
    src/RooJohnson.cxx
    src/Initialisation.cxx
  DICTIONARY_OPTIONS
    "-writeEmptyRootPCM"
  LINKDEF
    LinkDef1.h
  DEPENDENCIES
    Core
    RooFitCore
    Tree
    RIO
    Matrix
    MathCore
)

ROOT_LINKER_LIBRARY(RooFitCompute src/RooFitComputeLib.cxx TYPE SHARED DEPENDENCIES RooFit)

# RooFitComputeLib.cxx is already included in the RooFit package once, as a default architecture generic implementation.
# If roofit-arch-libs is enabled (see CMakeLists.txt in the parent directory), we want to compile the file again as a seperate library.
# For each copy we give the corresponding flags and at runtime, when cpu detection takes place the correct library is loaded dynamically.
if (roofit-arch-libs)
  ROOT_LINKER_LIBRARY(RooFitCompute_SSE4.1  src/RooFitComputeLib.cxx TYPE SHARED DEPENDENCIES RooFit)
  ROOT_LINKER_LIBRARY(RooFitCompute_AVX     src/RooFitComputeLib.cxx TYPE SHARED DEPENDENCIES RooFit)
  ROOT_LINKER_LIBRARY(RooFitCompute_AVX2    src/RooFitComputeLib.cxx TYPE SHARED DEPENDENCIES RooFit)

  # Add -fno-signaling-nans, -fno-trapping-math and -O3 to enable autovectorization
  # of the RooFitComputeLibrary (especially needed for GCC).
  set(common-flags $<$<CXX_COMPILER_ID:GNU>:-fno-signaling-nans>)
  list(APPEND common-flags $<$<OR:$<CONFIG:Release>,$<CONFIG:RelWithDebInfo>>: -fno-trapping-math -O3>)
  
  target_compile_options(RooFitCompute_SSE4.1  PRIVATE ${common-flags} -msse4    -DRF_ARCH=SSE4)
  target_compile_options(RooFitCompute_AVX     PRIVATE ${common-flags} -mavx     -DRF_ARCH=AVX )
  target_compile_options(RooFitCompute_AVX2    PRIVATE ${common-flags} -mavx2    -DRF_ARCH=AVX2)

  if(vdt AND NOT builtin_vdt)
    target_include_directories(RooFitCompute_SSE4.1    PRIVATE ${VDT_INCLUDE_DIR})
    target_include_directories(RooFitCompute_AVX       PRIVATE ${VDT_INCLUDE_DIR})
    target_include_directories(RooFitCompute_AVX2      PRIVATE ${VDT_INCLUDE_DIR})
  endif()

  # AVX512 is only supported in gcc 6+
  # We focus on AVX512 capable processors that support at least the skylake-avx512 instruction sets.
  if(NOT (CMAKE_CXX_COMPILER_ID STREQUAL "GNU") OR CMAKE_CXX_COMPILER_VERSION VERSION_GREATER_EQUAL 6)
    ROOT_LINKER_LIBRARY(RooFitCompute_AVX512  src/RooFitComputeLib.cxx TYPE SHARED DEPENDENCIES RooFit)
    target_compile_options(RooFitCompute_AVX512  PRIVATE ${common-flags} -march=skylake-avx512 -DRF_ARCH=AVX512)
    if(vdt AND NOT builtin_vdt)
      target_include_directories(RooFitCompute_AVX512 PRIVATE ${VDT_INCLUDE_DIR})
    endif()
  endif()

endif() #roofit-arch-libs

if(vdt AND NOT builtin_vdt)
  target_include_directories(RooFit PRIVATE ${VDT_INCLUDE_DIR})
  target_include_directories(RooFitCompute PRIVATE ${VDT_INCLUDE_DIR})
endif()

ROOT_ADD_TEST_SUBDIRECTORY(test)
