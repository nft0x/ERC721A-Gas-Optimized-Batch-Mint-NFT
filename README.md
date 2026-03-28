# ERC721A-Gas-Optimized-Batch-Mint-NFT
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "erc721a/contracts/ERC721A.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract GasOptimizedNFT is ERC721A, Ownable {
    uint256 public maxSupply = 10000;
    uint256 public mintPrice = 0.01 ether;
    string public baseURI;

    constructor(string memory name, string memory symbol, address initialOwner)
        ERC721A(name, symbol)
        Ownable(initialOwner)
    {}

    function mint(uint256 quantity) external payable {
        require(totalSupply() + quantity <= maxSupply, "Exceeds max supply");
        require(msg.value >= mintPrice * quantity, "Insufficient ETH");
        _safeMint(msg.sender, quantity);
    }

    function _baseURI() internal view override returns (string memory) {
        return baseURI;
    }

    function setBaseURI(string memory _newBaseURI) external onlyOwner {
        baseURI = _newBaseURI;
    }

    function withdraw() external onlyOwner {
        payable(owner()).transfer(address(this).balance);
    }
}
