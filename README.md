// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract MahokiGreenLungRevolution is ERC20, Ownable {
    // Maximaler Vorrat an Token (1.000.000.000 Tokens)
    uint256 public constant MAX_SUPPLY = 1000000000 * 10**18;  // 1 Milliarde Tokens mit 18 Dezimalstellen

    // Reserve-Adressen für die Tokenverteilung
    address public projectReserve;
    address public partnershipReserve;
    address public teamReserve;
    address public communityReserve;

    /**
     * @dev Konstruktor, der die Token erstellt und an die Reserve-Adressen verteilt.
     * @param _projectReserve Adresse des Projekt-Reserves
     * @param _partnershipReserve Adresse des Partnerschafts-Reserves
     * @param _teamReserve Adresse des Team-Reserves
     * @param _communityReserve Adresse des Community-Reserves
     * @param initialSupply Gesamtzahl der Token (inkl. Dezimalstellen)
     */
    constructor(
        address _projectReserve,
        address _partnershipReserve,
        address _teamReserve,
        address _communityReserve,
        uint256 initialSupply
    ) ERC20("Mahoki Green Lung Revolution", "MGLR") {
        // Überprüfen, ob keine der Adressen die Nulladresse ist
        require(_projectReserve != address(0), "Ungültige Projektadresse");
        require(_partnershipReserve != address(0), "Ungültige Partnerschaftsadresse");
        require(_teamReserve != address(0), "Ungültige Teamadresse");
        require(_communityReserve != address(0), "Ungültige Communityadresse");

        // Sicherstellen, dass der initiale Supply innerhalb des zulässigen Rahmens liegt
        require(initialSupply <= MAX_SUPPLY, "Initiale Versorgung überschreitet den maximalen Vorrat");

        projectReserve = _projectReserve;
        partnershipReserve = _partnershipReserve;
        teamReserve = _teamReserve;
        communityReserve = _communityReserve;

        // Minten des Gesamtvorrats an den Vertragsinhaber (Owner)
        _mint(msg.sender, initialSupply);

        // Beispielhafte Verteilung der Token
        // (Diese Logik kann an Ihre Anforderungen angepasst werden.)
        uint256 projectAmount = (initialSupply * 40) / 100;       // 40%
        uint256 partnershipAmount = (initialSupply * 30) / 100;   // 30%
        uint256 teamAmount = (initialSupply * 20) / 100;          // 20%
        uint256 communityAmount = initialSupply - projectAmount - partnershipAmount - teamAmount; // 10%

        // Übertragung der Token an die Reserve-Adressen
        _transfer(msg.sender, projectReserve, projectAmount);
        _transfer(msg.sender, partnershipReserve, partnershipAmount);
        _transfer(msg.sender, teamReserve, teamAmount);
        _transfer(msg.sender, communityReserve, communityAmount);
    }

    /**
     * @dev Override von _mint, um sicherzustellen, dass der maximale Vorrat nicht überschritten wird.
     */
    function _mint(address account, uint256 amount) internal override {
        require(totalSupply() + amount <= MAX_SUPPLY, "Minting würde den maximalen Vorrat überschreiten");
        super._mint(account, amount);
    }
}
