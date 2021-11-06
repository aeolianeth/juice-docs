# IJBETHPaymentTerminal

```solidity
interface IJBETHPaymentTerminal {
  event AddToBalance(uint256 indexed projectId, uint256 value, string memo, address caller);
  event Migrate(uint256 indexed projectId, IJBTerminal indexed to, uint256 amount, address caller);
  event DistributePayouts(
    uint256 indexed fundingCycleId,
    uint256 indexed projectId,
    address projectOwner,
    uint256 amount,
    uint256 tappedAmount,
    uint256 feeAmount,
    uint256 projectOwnerTransferAmount,
    string memo,
    address caller
  );

  event UseAllowance(
    uint256 indexed fundingCycleId,
    uint256 indexed configuration,
    uint256 indexed projectId,
    address beneficiary,
    uint256 amount,
    uint256 feeAmount,
    uint256 transferAmount,
    address caller
  );
  event ProcessFees(uint256 indexed projectId, JBFee[] fees);
  event Pay(
    uint256 indexed fundingCycleId,
    uint256 indexed projectId,
    address indexed beneficiary,
    JBFundingCycle fundingCycle,
    uint256 amount,
    uint256 weight,
    uint256 tokenCount,
    string memo,
    address caller
  );
  event RedeemTokens(
    uint256 indexed fundingCycleId,
    uint256 indexed projectId,
    address indexed holder,
    JBFundingCycle fundingCycle,
    address beneficiary,
    uint256 tokenCount,
    uint256 claimedAmount,
    string memo,
    address caller
  );
  event DistributeToPayoutSplit(
    uint256 indexed fundingCycleId,
    uint256 indexed projectId,
    JBSplit split,
    uint256 amount,
    address caller
  );

  function projects() external view returns (IJBProjects);

  function splitsStore() external view returns (IJBSplitsStore);

  function directory() external view returns (IJBDirectory);

  function heldFeesOf(uint256 _projectId) external view returns (JBFee[] memory);

  function distributePayoutsOf(
    uint256 _projectId,
    uint256 _amount,
    uint256 _currency,
    uint256 _minReturnedWei,
    string memory _memo
  ) external returns (uint256);

  function redeemTokensOf(
    address _holder,
    uint256 _projectId,
    uint256 _count,
    uint256 _minReturnedWei,
    address payable _beneficiary,
    string calldata _memo,
    bytes calldata _delegateMetadata
  ) external returns (uint256 claimedAmount);

  function useAllowanceOf(
    uint256 _projectId,
    uint256 _amount,
    uint256 _currency,
    uint256 _minReturnedWei,
    address payable _beneficiary
  ) external returns (uint256 fundingCycleNumber);

  function migrate(uint256 _projectId, IJBTerminal _to) external;
}
```