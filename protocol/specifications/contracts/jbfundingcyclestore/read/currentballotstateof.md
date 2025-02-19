# currentBallotStateOf

Contract:[`JBFundingCycleStore`](../)​‌

Interface: `IJBFundingCycleStore`

{% tabs %}
{% tab title="Step by step" %}
**The currency ballot state of the project.**

# Definition

```solidity
function currentBallotStateOf(uint256 _projectId) external view override returns (JBBallotState) { ... } 
```

* Arguments:
  * `_projectId` is the ID of the project to check the ballot state of.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The function overrides a function definition from the `IJBFundingCycleStore` interface.
* The function returns a [`JBBallotState`](../../../enums/jbballotstate.md).

# Body

1.  Get a reference to the latest funding cycle for the `_projectId`.

    ```solidity
    // Get a reference to the latest funding cycle configuration.
    uint256 _fundingCycleConfiguration = latestConfigurationOf[_projectId];
    ```

    _Internal references:_

    * [`latestConfigurationOf`](../properties/latestconfigurationof.md)
2.  Check that there is a funding cycle for the project.

    ```solidity
    // The project must have funding cycles.
    require(_fundingCycleConfiguration > 0, '0x14: NOT_FOUND');
    ```
3.  Get a reference to the funding cycle for the latest funding cycle.

    ```solidity
    // Resolve the funding cycle for the for the latest configuration.
    JBFundingCycle memory _fundingCycle = _getStructFor(_projectId, _fundingCycleConfiguration);
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)
4.  If this is the first funding cycle for the project, it must be approved.

    ```solidity
    // If the latest funding cycle is the first, or if it has already started, it must be approved.
    if (_fundingCycle.basedOn == 0) return JBBallotState.Approved;
    ```
5.  Return the `_ballotStateOf` the latest funding cycle configuration as is determined by the current configuration and the funding cycle it's based on.

    ```solidity
    return _ballotStateOf(_projectId, _fundingCycle.configuration, _fundingCycle.basedOn);
    ```

    _Internal references:_

    * [`_ballotStateOf`](\_ballotstateof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  The current ballot state of the project.

  @param _projectId The ID of the project to check the ballot state of.

  @return The current ballot's state.
*/
function currentBallotStateOf(uint256 _projectId) external view override returns (JBBallotState) {
  // Get a reference to the latest funding cycle configuration.
  uint256 _fundingCycleConfiguration = latestConfigurationOf[_projectId];
  
  // The project must have funding cycles.
  require(_fundingCycleConfiguration > 0, '0x14: NOT_FOUND');

  // Resolve the funding cycle for the for the latest configuration.
  JBFundingCycle memory _fundingCycle = _getStructFor(_projectId, _fundingCycleConfiguration);

  // If the latest funding cycle is the first, or if it has already started, it must be approved.
  if (_fundingCycle.basedOn == 0) return JBBallotState.Approved;

  return _ballotStateOf(_projectId, _fundingCycle.configuration, _fundingCycle.basedOn);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                | Description                                                  |
| --------------------- | ------------------------------------------------------------ |
| **`0x14: NOT_FOUND`** | Thrown if the provided project doesn't have a funding cycle. |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
