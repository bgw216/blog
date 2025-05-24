---
theme: seriph
background: https://images.unsplash.com/photo-1520022911530-fea50671df2e?q=80&w=2069&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D
title: Single Responsibility Principle, SRP
---

# 单一职责原则

敏捷软件开发原则

---

# 软件开发中的挑战有哪些？

<v-clicks>

* 复杂（业务复杂、技术实现复杂）
* 变化（业务/需求/功能的变化）

</v-clicks>

<!--
一个个动画逐步引入；
更难的是复杂加上变化
-->

---

# 软件生命周期代码呈现的趋势

<v-clicks>

* 僵化(Rigidity)：很难对系统进行改动，因为每处改动都会迫使系统其它部分进行许多改动。 
* 脆弱(Fragility)：对系统的改动会破坏在概念上与改动本身无关的其它地方。
* 牢固(Immobility)：很难把系统的缠结解开，成为可被其它系统复用的组件。
* 粘滞(Viscosity)：做正确的事情比做错误的事情要困难。
* 不必要的复杂性(Needless Complexity) 
* 不必要的重复(Needless Repetition)
* 晦涩(Opacity)：很难阅读、理解，没有很好地表达出意图。

</v-clicks>

<!--
一个个动画逐步引入；
概括起来，就是代码逐渐腐化
-->

---

# 如何应对代码的腐化

<v-clicks>

* 推倒重来
* 应用敏捷设计，适应变化，避免/减轻腐化。（重构）

</v-clicks>

<!--

-->

---

# 敏捷设计？

一个持续应用原则、模式以及实践来改进软件结构和可读性的过程。

体现在：

* 敏捷：以增量/迭代方式。
* 设计：设计出灵活（应对变化）、可维护（应对复杂度）、可重用（应对两者）的良好结构体。

---

# 敏捷设计原则中的基础原则：SOLID

* 单一职责原则（Single Responsibility Principle, SRP）
* 开放-封闭原则（Open/Closed Principle, OCP）  
* 里氏替换原则（Liskov Substitution Principle, LSP）  
* 接口隔离原则（Interface Segregation Principle, ISP）  
* 依赖倒置原则（Dependency Inversion Principle, DIP）

<!--
还有其它原则， 重用发布等价原则（REP）、无环依赖原则（ADP）等
可以提一提DRY
今天的重点在SRP，转入下一页
-->

---

# SRP的核心思想

一个“单元”（类、模块、方法、子系统等）应<strong style="color: green">仅有一个</strong>引起变化的原因。

<blockquote>每一个职责都是变化的一个轴线。当需求变化时，该变化会反映为类职责的变化。</blockquote>

变化是职责划分的核心驱动力。
职责的定义应基于“变化的维度”，即每个职责应聚焦于应对某一类变化。
因此，理解职责的粒度与边界是应用SRP的关键。

<!--
1. 重点强调仅有一个
2. 针对单元的解释
3. 针对变化的解释

？怎样承上启下，与职责的关系
变化与职责
-->

---

# 职责的粒度控制

关注：职责的粒度控制，职责划分应基于“同一变化维度”。
判断标准：若一个类因多个不相关的需求（如价格计算规则变化与物流信息格式调整）被修改，则需拆分职责。
误区：并非要求“一个方法只写一行代码”，而是关注职责的业务逻辑边界。

---

# SRP解决了哪些问题

* 高耦合与低内聚：修改某一功能时可能影响其他功能。
* 维护困难：混杂多个职责，修改时需理解无关逻辑，易引入错误。
* 可测试性差：多职责类需覆盖复杂场景，测试用例冗余且难以聚焦。
* 复用性低：功能混杂的类难以被其他模块复用。
* 抵御变更能力弱：需求变化导致频繁修改混杂职责的“单元”

---

# 示例1 - 违反 SRP

```tsx
class Weather extends Component {
  state = { temperature: 'N/A' };
  
  componentDidMount() {
    axios.get('http://weather.com/api').then(response => {
      this.setState({ temperature: response.data.current.temperature });
    });
  }


  render() {
    return <div>温度：{this.state.temperature}°C</div>;
  }
}
```

---

# 示例1 - 符合 SRP 的改进

```tsx
// 职责：数据获取
class WeatherFetch extends Component {
  state = { temperature: 'N/A' };
  
  componentDidMount() {
    axios.get('http://weather.com/api').then(response => {
      this.setState({ temperature: response.data.current.temperature });
    });
  }


  render() {
    return <WeatherInfo temperature={this.state.temperature} />;
  }
}


// 职责：数据展示
const WeatherInfo = ({ temperature }) => (
  <div>温度：{temperature}°C</div>
);

```

---

# 示例2 - 违反 SRP

```tsx
const ActiveUsersList = () => {
  const [users, setUsers] = useState([]);
  
  useEffect(() => {
    fetchUsers().then(data => setUsers(data));
  }, []);


  return (
    <ul>
      {users.filter(user => user.isActive).map(user => (
        <li key={user.id}>
          {user.name} - {user.email}
        </li>
      ))}
    </ul>
  );
};
```

---

# 示例2 - 符合 SRP 的改进

```tsx
// 职责：数据获取（自定义 Hook）
const useActiveUsers = () => {
  const [users, setUsers] = useState([]);
  useEffect(() => { fetchUsers().then(setUsers); }, []);
  return users.filter(user => user.isActive);
};

// 职责：用户项展示
const UserItem = ({ user, onClick }) => (
  <li>
    {user.name} - {user.email}
  </li>
);

// 职责：列表容器
const ActiveUsersList = () => {
  const users = useActiveUsers();
  return (
    <ul>
      {users.map(user => <UserItem key={user.id} user={user} />)}
    </ul>
  );
};
```

---
layout: center
---

# 讨论 & 交流

---
layout: center
---

# Thanks

---

# 参考资料

* [《敏捷软件开发 原则、模式与实践》](https://book.douban.com/subject/1140457/)
