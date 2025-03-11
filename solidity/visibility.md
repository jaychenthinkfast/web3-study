在 Solidity 中，函数和状态变量的可见性（visibility）决定了它们可以被访问的范围。Solidity 提供了四种可见性修饰符：**public**、**private**、**internal** 和 **external**。以下是它们的区别和详细说明：

### 1. **public（公共）**

* **定义**: 任何人都可以访问，包括合约内部、外部调用者（通过交易）以及继承的合约。
* **特点**:

  * 对于状态变量，Solidity 会自动生成一个 getter 函数，外部可以通过这个函数访问变量。
  * 对于函数，外部可以通过交易调用，内部也可以直接调用。
* **适用场景**: 需要让外部用户或合约访问的状态变量或函数。
* **示例**:
  **solidity**

  ```
  uint public myNumber = 42; // 自动生成 getter 函数
  function getNumber() public view returns (uint) {
      return myNumber;
  }
  ```

### 2. **private（私有）**

* **定义**: 仅限于当前合约内部访问，外部和继承的合约都无法直接访问。
* **特点**:

  * 提供了最高的封装性。
  * 对于状态变量，不会生成 getter 函数。
  * 对于函数，只有当前合约内的代码可以调用。
* **适用场景**: 敏感数据或逻辑，不希望被外部或子合约访问。
* **示例**:
  **solidity**

  ```
  uint private secretNumber = 100;
  function getSecret() private view returns (uint) {
      return secretNumber;
  }
  ```

### 3. **internal（内部）**

* **定义**: 仅限于当前合约及其继承的子合约访问，外部无法调用。
* **特点**:

  * 类似于面向对象编程中的 **protected**。
  * 不会暴露给外部调用，但子合约可以通过继承访问。
* **适用场景**: 需要在合约家族内部共享，但不对外公开的功能或变量。
* **示例**:
  **solidity**

  ```
  uint internal familyNumber = 200;
  function getFamilyNumber() internal view returns (uint) {
      return familyNumber;
  }
  ```

### 4. **external（外部）**

* **定义**: 只能被外部调用（通过交易），但不能直接在合约内部调用（除非显式使用 **this**）。
* **特点**:

  * 优化了 gas 成本，因为外部调用时参数以 calldata 形式传递，而不是复制到内存。
  * 内部调用需要使用 **this.functionName()**，但不推荐这样做（会增加 gas 成本）。
* **适用场景**: 专门设计给外部调用的函数，尤其是需要接收大量数据的场景。
* **示例**:
  **solidity**

  ```
  function externalFunction(uint _value) external returns (uint) {
      return _value + 1;
  }
  ```

---

### **对比表格**


| 可见性       | 合约内部访问     | 继承合约访问 | 外部访问 | 自动生成 Getter（状态变量） | 适用场景               |
| ------------ | ---------------- | ------------ | -------- | --------------------------- | ---------------------- |
| **public**   | 是               | 是           | 是       | 是                          | 公开数据或功能         |
| **private**  | 是               | 否           | 否       | 否                          | 私有数据或逻辑         |
| **internal** | 是               | 是           | 否       | 否                          | 合约家族内部共享       |
| **external** | 否（需**this**） | 否           | 是       | 不适用                      | 外部专用函数，优化 gas |

---

### **注意事项**

1. **默认可见性**:
   * 如果不指定可见性，状态变量默认为 **internal**，而函数默认为 **public**（但建议显式声明，避免意外）。
2. **Gas 成本**:
   * **external** 函数在外部调用时更省 gas，因为它直接使用 **calldata**。
   * **public** 函数在内部和外部调用时可能需要额外的内存分配。
3. **安全性**:
   * 使用 **private** 或 **internal** 可以防止意外暴露数据或逻辑，但区块链数据本身是公开的，**private** 仅限制直接访问，不代表数据完全隐藏。
