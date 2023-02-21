# Web3

## solidity

## solidity storage

solidity storage 的每一个 slot 是 32Bytes

    contract FunWithStorage{
        //顺序存储在storage中
        uint256 favouriteNumber;
        bool someBool;
        uint256[] myArray;
        uint256 constant NOT_IN_STORAGE = 123;//const和immutable不存在storage中，因为他们直接写在contract的bytecode中

        constructor(){
            favouriteNumber = 25;
            someBool = true;
            myArray.push(222);//每次插入一项，做hash
        }

        function doStuff() public {
            uint256 newVar = favouriteNumber+1;
            uint256 otherVar = 7;
            //方法中的变量不存在storage中，存在memory中，方法执行完就删了
        }
    }

Storage  
[0] 0x00..19  
[1] 0x00..01  
[2] 0x00..01 存数组的长度  
[3] 如果是 mapping 则留白  
[keccak256(2)]0x00..0de  
如果想节省一点 gas，可以把少读 storage，读一次进 memory 后重复读 memory(mappings 不能存进 memory，sorry！)因为读写 storage 要花很多 gas(SLOAD 花 800 SSTORE 花 20000)

## storage vs memory

storage 是存在 blockchain 上，memory 是存本地，用完即删.
用了 storage 关键字的变量会指向 blockchain 中的那个对象，若修改即为修改 blockchain 上存储的东西

## 小游戏

https://cryptozombies.io/

## youtube

https://www.youtube.com/watch?v=gyMwXuJrbJQ
