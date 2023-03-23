# 用 Rust 实现的稳定的人工智能优化算法

> 原文：<https://betterprogramming.pub/a-stable-ai-optimization-algorithm-c2a1467f2b1b>

## **使用人工蜂群算法**

![](img/87f514ae55ec55594fa27ad7a861abf0.png)[](https://medium.com/@applied-math-coding/membership) [## 通过我的推荐链接加入 Medium-applied . math . coding

### 获得我所有的故事，以及其他作家在媒体上发表的成千上万的故事。我坚信，媒介是…

medium.com](https://medium.com/@applied-math-coding/membership) 

人工智能中的大多数技术。机器学习需要解决一个连续的优化问题。通常，这种优化问题是非线性的，不能手工解决。存在大量关于这类优化问题和有效解决它们的可能方法的文献。

这些方法之间的主要区别可以通过使用导数来区分。例如，顾名思义，基于梯度的方法需要计算导数。这同样适用于牛顿法，它试图寻找一阶导数的根。

众所周知，如果有效的话，这些方法会很快见效。但是它们有时也完全不起作用，或者很难陷入局部极小值。

在这个故事中，我想介绍一种本身基于人工智能的优化技术。就像大多数基于人工智能的算法一样，它是不确定的。

# 该算法

它的名字是“**人工蜂群算法**”，它遵循自然界中观察到的关于蜜蜂的模式:

固定数量的 N 个“蜜蜂”被随机地(均匀地)放置在域中。每只蜜蜂记忆一个基于当前的位置，算法记忆一个全局最佳位置。

**局部搜索:**在每一步中，每只蜜蜂首先通过随机选择另一只蜜蜂并朝着或远离它的对手移动随机距离来试图找到一个更好的位置。它只在新位置比当前位置更好的情况下执行实际移动。

**旁观者阶段:**当所有的蜜蜂都完成了局部搜索后，它们会根据一个适合度值通过分类分布进行重新定位。具体来说，通过计算每只蜜蜂的值与当前“最差”蜜蜂的值之间的距离，为每只蜜蜂分配一个适应度。所以，这个距离越高，健身效果越好。我们认为作为一个类别的每个适应值通过将适应值除以所有适应值的总和来加权。这提供了所有适合度值的分布(分类),我们可以从中抽取随机样本。请注意，适合度越高，样本从相应类别中被选中的可能性就越大。这样做 N 次，实际上有助于 N 次选择一些蜜蜂，我们为每只蜜蜂找到一个新的位置。这样，一个位置的适应值越好，分配到那里的蜜蜂就越多。

**侦察阶段:**在此之后，所有没有通过重复上述移动 X 次来提高其当前值的蜜蜂，被随机地重新定位到整个域中，与初始化时的方式相同。

为了提高全局最优解，上述过程重复几次。

此外，这种方法非常稳定，它适用于高维，只要求函数是连续的，并且非常容易实现。

此外，它允许许多自定义改进。很难估计它的收敛速度，但在许多应用中已经证实，它在搜索全局最小值时工作得特别好，而不会陷入局部最小值。

因此，如果需要该全局最小值的非常精确的解，最好使用这种方法，让它接近该点，然后用最陡梯度或牛顿迭代来产生所需的精度。

包括发明人在内的更多详细信息可在以下网址找到:

> 人工蜂群算法。(2022 年 7 月 8 日)。在*维基百科*里。[https://en.wikipedia.org/wiki/Artificial_bee_colony_algori](https://en.wikipedia.org/wiki/Artificial_bee_colony_algorithm)

# 履行

我们将看看 Rust 中可能的实现。对于那些需要对 Rust 做一些总结的人，我推荐阅读[我的](https://medium.com/@applied-math-coding/list/an-introduction-into-rust-22c99777c5e5)简介。

为了产生随机值，我们把箱子`[rand](https://crates.io/crates/rand)`放到我们的依赖项(`Cargo.toml`)中:

```
[dependencies]
rand = { version = "0.8.5" }
```

在整个计划中，我们使用以下类型:

```
use rand::{distributions::WeightedIndex,
 prelude::Distribution, rngs::ThreadRng, thread_rng, Rng};

pub struct Params {
    n_bees: usize,
    abandon_limit: usize,
    max_iter: usize,
}

type X = Vec<f64>;
type Target = dyn Fn(&X) -> f64;

struct Bee {
    position: X,
    value: f64,
    not_improved_since: usize,
    fitness: f64, // distance to worst
}
```

所以，一辆`Bee`由它的`position`、`value, fitness`以及它多久没能改进它的`value`来描述。`X`只是一个方便的占位符，而`Target`是我们想要最小化的函数类型。

接下来，我们列出一些需要的简单实用函数:

```
fn compute_fitness_on_bees(bees: &mut Vec<Bee>) {
    let worst_value = bees[find_worst_bee_idx(bees)].value;
    for bee in bees.iter_mut() {
        bee.fitness = worst_value - bee.value;
    }
}

// returns the best position and value among all the bees
fn find_best_position_and_value(bees: &Vec<Bee>) -> (X, f64) {
    let best_bee = bees
        .iter()
        .min_by(|b1, b2| b1.value.partial_cmp(&b2.value).unwrap())
        .unwrap();
    (best_bee.position.clone(), best_bee.value)
}

// returns the index of the bee with highest (worst) value
fn find_worst_bee_idx(bees: &Vec<Bee>) -> usize {
    bees.iter()
        .enumerate()
        .max_by(|(_, b1), (_, b2)| b1.value.partial_cmp(&b2.value).unwrap())
        .unwrap()
        .0
}

// creates a random point within the n-dim. interval [a, b],
// that is a </= x </= b
fn create_random_position(a: &X, b: &X, rng: &mut ThreadRng) -> X {
    let mut res = vec![];
    for (u, v) in a.iter().zip(b.iter()) {
        res.push(rng.gen_range(*u..*v));
    }
    res
}
```

最后，我们按照算法中的描述逐一实现每个阶段。你会看到，所有的步骤都很容易实现:

```
// create n bees and position them randomly in [a,b]
fn init_bees(f: &Target, a: &X, b: &X, n_bees: usize) -> Vec<Bee> {
    let mut res = vec![];
    let mut rng = thread_rng();
    for _ in 0..n_bees {
        let position = create_random_position(a, b, &mut rng);
        res.push(Bee {
            fitness: 0.0,
            not_improved_since: 0,
            value: f(&position),
            position,
        });
    }
    res
}

// let each bee locally search by moving a random distance to or away from
// another random bee.
// At the same time, keep the globally best know value/position up to date.
fn do_local_search_phase(
    f: &Target,
    bees: &mut Vec<Bee>,
    best_position: &mut X,
    best_value: &mut f64,
    a: &X,
    b: &X,
) {
    let mut rng = thread_rng();
    for idx in 0..bees.len() {
        let mut new_position = bees.get(idx).unwrap().position.clone();
        let other_bee_idx = rng.gen_range(0..bees.len());
        let position_idx = rng.gen_range(0..new_position.len());
        let phi = rng.gen_range(-1.0..1.0);
        let x_i_other = bees.get(other_bee_idx).unwrap()
                   .position[position_idx];
        let x_i = new_position[position_idx];
        new_position[position_idx] = (x_i + phi * (x_i_other - x_i))
            .max(a[position_idx])
            .min(b[position_idx]);
        let new_value = f(&new_position);
        let old_value = bees.get(idx).unwrap().value;
        let bee = bees.get_mut(idx).unwrap();
        if new_value < old_value {
            bee.position = new_position;
            bee.value = new_value;
            bee.not_improved_since = 0;
        } else {
            bee.not_improved_since += 1;
        }
        if new_value < *best_value {
            *best_value = bee.value;
            *best_position = bee.position.clone();
        }
    }
}

// Re-distribute bees onto other locations and prioritize locations with
// higher fitness. This is done by laying a categorical distrubition over all
// the bees.
fn do_onlooker_phase(bees: &mut Vec<Bee>) {
    compute_fitness_on_bees(bees);
    let sum_fitness = bees.iter().map(|bee| bee.value).sum::<f64>();
    let weights = bees
        .iter()
        .map(|bee| bee.value / sum_fitness)
        .collect::<X>();
    let categorical_dist = WeightedIndex::new(&weights).unwrap();
    let mut rng = thread_rng();
    for idx in 0..bees.len() {
        let other_bee = bees.get(categorical_dist.sample(&mut rng)).unwrap();
        let new_position = other_bee.position.clone();
        let new_value = other_bee.value;
        bees[idx].position = new_position;
        bees[idx].value = new_value;
    }
}

// Abandon bees from positions if have not been improved for long.
// This avoids being trapped at local minima.
fn do_scout_phase(bees: &mut Vec<Bee>, 
abandon_limit: usize, a: &X, b: &X, f: &Target) {
    let mut rng = thread_rng();
    bees.iter_mut()
        .filter(|bee| bee.not_improved_since > abandon_limit)
        .for_each(|bee| {
            let new_position = create_random_position(a, b, &mut rng);
            let new_value = f(&new_position);
            bee.position = new_position;
            bee.value = new_value;
            bee.not_improved_since = 0;
        });
}
```

现在，我们一起将这些阶段放入一个迭代中:

```
pub fn optimize(f: &Target, a: &X, b: &X, params: Params) -> (X, f64) {
    let mut bees = init_bees(f, a, b, params.n_bees);
    let (mut best_position, mut best_value) = find_best_position_and_value(&bees);
    for _ in 0..params.max_iter {
        do_local_search_phase(f, &mut bees, &mut best_position, &mut best_value, a, b);
        do_onlooker_phase(&mut bees);
        do_scout_phase(&mut bees, params.abandon_limit, a, b, f);
    }
    (best_position, best_value)
}
```

作为一个测试函数，我使用了 Ackley 函数(见这里的[和](https://en.wikipedia.org/wiki/Ackley_function))，这是一个有很多局部最小值的函数:

```
fn main(){
   let ackley_fn = |x: &X| {
            -20.0 * f64::exp(
               -0.2 * f64::sqrt(0.5 * (x[0] * x[0] + x[1] * x[1]))
             ) - f64::exp(
               0.5 * (f64::cos(2.0 * PI * x[0]) + f64::cos(2.0 * PI * x[1]))
             ) + E + 20.0
        };

        println!("{}", ackley_fn(&vec![0.0, 0.0])); // this is the minimum

        let params = Params {
            n_bees: 200,
            abandon_limit: 20,
            max_iter: 500,
        };
        println!(
            "{:?}",
            optimize(&ackley_fn, &vec![-5.0, -5.0], &vec![5.0, 5.0], params)
        );
}
```

输出是:

```
([0.007315634561002593, 9.547793830446227e-5], 0.02211822639045735)
```

你可以在这里找到关于[的全部代码。](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=36db9de9533ff56b869d543696fff288)

感谢阅读！