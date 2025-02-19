# createFor

Contract: [`JBProjects`](../)

Interface: [`IJBProjects`](../../../interfaces/ijbprojects.md)

{% tabs %}
{% tab title="Step by step" %}
**Creates a new project for the specified owner, which mints an NFT (ERC-721) into their wallet.**

_Anyone can create a project on an owner's behalf._

# Definition

```solidity
function createFor(
  address _owner,
  bytes32 _handle,
  string calldata _metadataCid
) external override returns (uint256) { ... }
```

* Arguments:
  * `_owner` is the address that will be the owner of the project.
  * `_handle` is a unique string to associate with the project that will resolve to its token ID.
  * `_metadataCid` is an IPFS CID hash where metadata about the project has been uploaded. An empty string is acceptable if no metadata is being provided.
* The function can be accessed externally by anyone.
* The function overrides a function definition from the `IJBProjects` interface.
* Returns the token ID of the newly created project.

# Body

1.  Check that the provided `_handle` is not empty.

    ```solidity
    // Handle must exist.
    require(_handle != bytes32(0), '0x06: EMPTY_HANDLE');
    ```
2.  Check that the `_handle` is unique. This is done by making sure there isn't yet an `idFor` the handle, and making sure it isn't currently being transferred to an address.

    ```solidity
    // Handle must be unique.
    require(idFor[_handle] == 0 && transferAddressFor[_handle] == address(0), '0x07: HANDLE_TAKEN');
    ```

    _Internal references:_

    * [`idFor`](../properties/idfor.md)
    * [`transferAddressFor`](../properties/transferaddressfor.md)
3.  Increment the count to include the new project being created.

    ```solidity
    // Increment the count, which will be used as the ID.
    count++;
    ```

    _Internal references:_

    * [`count`](../properties/count.md)
4.  Mint a new NFT token belonging to the `_owner` using the `count` as the token ID.

    ```solidity
    // Mint the project.
    _safeMint(_owner, count);
    ```
5.  Store the provided `_handle` as the as the `handleOf` the newly created project.

    ```solidity
    // Store the handle for the project ID.
    handleOf[count] = _handle;
    ```

    _Internal references:_

    * [`handleOf`](../properties/handleof.md)
6.  Store the newly created project's ID as the `idFor` the provided `_handle` to allow for project lookup using the handle.

    ```solidity
    // Store the project ID for the handle.
    idFor[_handle] = count;
    ```

    _Internal references:_

    * [`idFor`](../properties/idfor.md)
7.  If a URI was provided (meaning it's not an empty string), store it as the `metadataCidOf` the newly created project.

    ```solidity
    // Set the URI if one was provided.
    if (bytes(_metadataCid).length > 0) metadataCidOf[count] = _metadataCid;
    ```

    _Internal references:_

    * [`metadataCidOf`](../properties/metadatacidof.md)
8.  Emit a `Create` event with all relevant parameters.

    ```
    emit Create(count, _owner, _handle, _metadataCid, _terminal, msg.sender);
    ```

    _Event references:_

    * [`Create`](../events/create.md)
9.  Return the newly created project's token ID.

    ```solidity
    return count;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Create a new project for the specified owner, which mints an NFT (ERC-721) into their wallet.

  @dev 
  Anyone can create a project on an owner's behalf.

  @param _owner The address that will be the owner of the project.
  @param _handle A unique string to associate with the project that will resolve to its token ID.
  @param _metadataCid An IPFS CID hash where metadata about the project has been uploaded. An empty string is acceptable if no metadata is being provided.

  @return The token ID of the newly created project
*/
function createFor(
  address _owner,
  bytes32 _handle,
  string calldata _metadataCid
) external override returns (uint256) {
  // Handle must exist.
  require(_handle != bytes32(0), '0x06: EMPTY_HANDLE');

  // Handle must be unique.
  require(idFor[_handle] == 0 && transferAddressFor[_handle] == address(0), '0x07: HANDLE_TAKEN');

  // Increment the count, which will be used as the ID.
  count++;

  // Mint the project.
  _safeMint(_owner, count);

  // Store the handle for the project ID.
  handleOf[count] = _handle;

  // Store the project ID for the handle.
  idFor[_handle] = count;

  // Set the URI if one was provided.
  if (bytes(_metadataCid).length > 0) metadataCidOf[count] = _metadataCid;

  emit Create(count, _owner, _handle, _metadataCid, msg.sender);

  return count;
}
```
{% endtab %}

{% tab title="Errors" %}
| String                   | Description                                      |
| ------------------------ | ------------------------------------------------ |
| **`0x06: EMPTY_HANDLE`** | Thrown if the provided handle is empty           |
| **`0x07: HANDLE_TAKEN`** | Thrown if the provided handle is already in use. |
{% endtab %}

{% tab title="Events" %}
| Name                                | Data                                                                                                                                                                                                                |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Create`**](../events/create.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed owner</code></li><li><code>bytes32 indexed handle</code></li><li><code>string uri</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
