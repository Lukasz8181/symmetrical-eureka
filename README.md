- 👋 Hi, I’m @Lukasz8131
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
Lukasz8181/Lukasz8181 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
// Kontrakt dla tokenu Bitcoin Minetrix
pragma solidity ^0.8.0;

contract BitcoinMinetrixToken {
    // Szczegóły tokenu
    string public name = "Bitcoin Minetrix";
    string public symbol = "BMX";
    uint8 public decimals = 18; // 18 miejsc po przecinku (tak jak w Ethereum)

    // Całkowita ilość tokenów
    uint256 public totalSupply;

    // Mapowanie sald dla każdego adresu
    mapping(address => uint256) public balanceOf;

    // Mapowanie pozwoleń (dla delegowanych transferów)
    mapping(address => mapping(address => uint256)) public allowance;

    // Wydarzenia dla transferu i zatwierdzenia
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);

    // Konstruktor: Inicjalizuje całkowitą ilość i przypisuje wszystkie tokeny do twórcy kontraktu
    constructor(uint256 _initialSupply) {
        totalSupply = _initialSupply * 10**uint256(decimals);
        balanceOf[msg.sender] = totalSupply;
    }

    // Przekazuje tokeny od nadawcy do odbiorcy
    function transfer(address _to, uint256 _value) public returns (bool success) {
        require(_to != address(0), "Nieprawidłowy adres odbiorcy");
        require(balanceOf[msg.sender] >= _value, "Niewystarczające saldo");

        balanceOf[msg.sender] -= _value;
        balanceOf[_to] += _value;

        emit Transfer(msg.sender, _to, _value);
        return true;
    }

    // Zatwierdza wydawanie tokenów przez uprawnionego
    function approve(address _spender, uint256 _value) public returns (bool success) {
        require(_spender != address(0), "Nieprawidłowy adres uprawnionego");

        allowance[msg.sender][_spender] = _value;

        emit Approval(msg.sender, _spender, _value);
        return true;
    }

    // Przekazuje tokeny od jednego adresu do drugiego (przez uprawnionego)
    function transferFrom(address _from, address _to, uint256 _value) public returns (bool success) {
        require(_from != address(0), "Nieprawidłowy adres nadawcy");
        require(_to != address(0), "Nieprawidłowy adres odbiorcy");
        require(balanceOf[_from] >= _value, "Niewystarczające saldo");
        require(allowance[_from][msg.sender] >= _value, "Przekroczono limit zatwierdzenia");

        balanceOf[_from] -= _value;
        balanceOf[_to] += _value;
        allowance[_from][msg.sender] -= _value;

        emit Transfer(_from, _to, _value);
        return true;
    }
}
