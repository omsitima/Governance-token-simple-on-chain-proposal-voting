# Governance-token-simple-on-chain-proposal-voting
Governance token + simple on‑chain proposal voting
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract GovToken is ERC20 {
    constructor() ERC20("GovToken", "GOV") {
        _mint(msg.sender, 1000 ether);
    }
}

contract SimpleGovernor {
    GovToken public gov;
    struct Proposal {
        string description;
        uint256 yes;
        uint256 no;
        bool executed;
    }

    Proposal[] public proposals;
    mapping(uint256 => mapping(address => bool)) public voted;

    constructor(address _gov) {
        gov = GovToken(_gov);
    }

    function createProposal(string memory desc) public {
        proposals.push(Proposal(desc, 0, 0, false));
    }

    function vote(uint256 id, bool support) public {
        require(!voted[id][msg.sender], "Already voted");
        uint256 weight = gov.balanceOf(msg.sender);
        require(weight > 0, "No voting power");

        voted[id][msg.sender] = true;
        if (support) proposals[id].yes += weight;
        else proposals[id].no += weight;
    }
}
