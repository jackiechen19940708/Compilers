Analysis Pass需要一个静态AnalysisKey去标记地址
// A special type used by analysis passes to provide an address that
  // identifies that particular analysis pass type.
  static llvm::AnalysisKey Key;
