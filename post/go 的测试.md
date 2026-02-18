---
author: Ryou
title: go 的测试
date: 2026-02-14T21:42:50+08:00
lastmod: 2026-02-14T21:50:00+08:00
description: 全面介绍 Go 语言测试的最佳实践、表驱动测试、测试框架和 Mock 技术
draft: true
mermaid: true
image: https://w.wallhaven.cc/full/og/wallhaven-ogy6zm.jpg
位置: 上海市 - 上海市
坐标: 
  - 121.4768
  - 31.2243
温度: 12.6℃ 
风速: 4.9 m/s
天气: 晴朗
categories:
  - Go
tags:
  - Go
  - 测试
  - 单元测试
  - TDD
---

> [!summary] 前情提要
> Go 语言内置了强大的 testing 包，配合表驱动测试（Table-Driven Tests）模式，可以编写简洁、高效的测试代码。本文将全面介绍 Go 测试的核心概念、最佳实践、常用框架和 Mock 技术。

## Go 测试基础

### 测试文件命名规则

- 测试文件必须以 `_test.go` 结尾
- 测试函数名必须以 `Test` 开头
- 测试函数签名：`func TestXxx(t *testing.T)`
- 基准测试以 `Benchmark` 开头：`func BenchmarkXxx(b *testing.B)`
- 示例测试以 `Example` 开头：`func ExampleXxx()`

### 基本测试示例

```go
// calculator.go
package calculator

func Add(a, b int) int {
    return a + b
}

func Divide(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("division by zero")
    }
    return a / b, nil
}
```

```go
// calculator_test.go
package calculator

import (
    "testing"
)

func TestAdd(t *testing.T) {
    result := Add(2, 3)
    if result != 5 {
        t.Errorf("Add(2, 3) = %d; want 5", result)
    }
}

func TestDivide(t *testing.T) {
    result, err := Divide(10, 2)
    if err != nil {
        t.Fatalf("unexpected error: %v", err)
    }
    if result != 5 {
        t.Errorf("Divide(10, 2) = %d; want 5", result)
    }
}
```

### 运行测试

```bash
# 运行当前包的所有测试
go test

# 运行所有子包的测试
go test ./...

# 运行特定测试函数
go test -run TestAdd

# 运行所有匹配前缀的测试
go test -run TestCalculate

# 显示详细输出
go test -v

# 运行基准测试
go test -bench=.

# 代码覆盖率
go test -cover
go test -coverprofile=coverage.out
go tool cover -html=coverage.out
```

## 表驱动测试（Table-Driven Tests）

表驱动测试是 Go 社区推荐的核心测试模式，将测试用例组织成表格形式，减少代码重复，提高可维护性。

### 基本结构

```go
func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive numbers", 2, 3, 5},
        {"negative numbers", -2, -3, -5},
        {"mixed numbers", -2, 3, 1},
        {"zero", 0, 0, 0},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := Add(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Add(%d, %d) = %d; want %d", 
                    tt.a, tt.b, result, tt.expected)
            }
        })
    }
}
```

### 使用 Map 组织测试用例

```go
func TestDivide(t *testing.T) {
    tests := map[string]struct {
        a, b        int
        expected    int
        expectedErr bool
    }{
        "normal division": {
            a: 10, b: 2, expected: 5, expectedErr: false,
        },
        "negative numbers": {
            a: -10, b: -2, expected: 5, expectedErr: false,
        },
        "division by zero": {
            a: 10, b: 0, expected: 0, expectedErr: true,
        },
    }

    for name, tt := range tests {
        t.Run(name, func(t *testing.T) {
            result, err := Divide(tt.a, tt.b)
            
            if tt.expectedErr {
                if err == nil {
                    t.Errorf("expected error, got nil")
                }
                return
            }
            
            if err != nil {
                t.Errorf("unexpected error: %v", err)
                return
            }
            
            if result != tt.expected {
                t.Errorf("Divide(%d, %d) = %d; want %d",
                    tt.a, tt.b, result, tt.expected)
            }
        })
    }
}
```

### 并行测试

```go
func TestParallel(t *testing.T) {
    tests := []struct {
        name string
        arg  int
    }{
        {"test1", 1},
        {"test2", 2},
        {"test3", 3},
    }

    for _, tt := range tests {
        tt := tt // 必须创建副本，避免闭包问题
        t.Run(tt.name, func(t *testing.T) {
            t.Parallel() // 标记为并行执行
            // 测试逻辑
            time.Sleep(time.Second)
            t.Logf("completed %s", tt.name)
        })
    }
}
```

### 表驱动测试最佳实践

| 实践 | 说明 |
|------|------|
| **保持表格聚焦** | 每个测试函数专注于相关用例，表格过大时拆分为多个函数 |
| **使用描述性名称** | 字段名如 `input`、`expected`、`wantErr` 提高可读性 |
| **处理 Panic** | 在子测试中使用 `t.Cleanup` 或 `recover` 处理可能 panic 的函数 |
| **并行化** | 添加 `t.Parallel()` 加速执行，但要确保线程安全 |
| **避免过度使用** | 简单测试不需要表驱动，保持简单 |
| **测试边界条件** | 包含 `nil`、零值、最大值、无效输入等边界情况 |
| **集成 CI/CD** | 确保测试在自动化流水线中运行 |

## 测试框架

### 1. Testify（最流行）

GitHub Stars: 23,000+ | 最广泛使用的 Go 测试工具包

**安装：**
```bash
go get github.com/stretchr/testify
```

**主要包：**
- `assert`：断言函数，失败继续执行
- `require`：断言函数，失败立即终止
- `mock`：Mock 框架
- `suite`：测试套件

**assert 示例：**
```go
import (
    "testing"
    "github.com/stretchr/testify/assert"
)

func TestWithTestify(t *testing.T) {
    result := Add(2, 3)
    
    // 基础断言
    assert.Equal(t, 5, result, "they should be equal")
    assert.NotEqual(t, 4, result)
    assert.True(t, result > 0)
    assert.Nil(t, err)
    assert.NotNil(t, obj)
    
    // 集合断言
    assert.Contains(t, []int{1, 2, 3}, 2)
    assert.Len(t, []int{1, 2, 3}, 3)
    
    // 错误断言
    assert.Error(t, err)
    assert.EqualError(t, err, "expected error")
    assert.NoError(t, err)
}
```

**require 示例：**（失败立即终止）
```go
import "github.com/stretchr/testify/require"

func TestWithRequire(t *testing.T) {
    obj, err := CreateObject()
    require.NoError(t, err) // 失败立即终止
    require.NotNil(t, obj)
    
    // 后续断言
    assert.Equal(t, "expected", obj.Name)
}
```

**测试套件：**
```go
import (
    "testing"
    "github.com/stretchr/testify/suite"
)

type CalculatorSuite struct {
    suite.Suite
    calc *Calculator
}

func (s *CalculatorSuite) SetupSuite() {
    // 整个套件执行前
}

func (s *CalculatorSuite) TearDownSuite() {
    // 整个套件执行后
}

func (s *CalculatorSuite) SetupTest() {
    // 每个测试前
    s.calc = NewCalculator()
}

func (s *CalculatorSuite) TearDownTest() {
    // 每个测试后
}

func (s *CalculatorSuite) TestAdd() {
    result := s.calc.Add(2, 3)
    s.Equal(5, result)
}

func TestCalculatorSuite(t *testing.T) {
    suite.Run(t, new(CalculatorSuite))
}
```

### 2. GoConvey（BDD 风格）

GitHub Stars: 8,000+ | Web UI + BDD 语法

**特点：**
- 行为驱动开发（BDD）风格的 DSL
- 自动刷新的 Web UI
- 代码覆盖率可视化
- 与 `go test` 完全集成

**安装：**
```bash
go get github.com/smartystreets/goconvey
go install github.com/smartystreets/goconvey/web/server
```

**使用示例：**
```go
import (
    "testing"
    . "github.com/smartystreets/goconvey/convey"
)

func TestSpec(t *testing.T) {
    Convey("Given a calculator", t, func() {
        calc := NewCalculator()
        
        Convey("When adding two numbers", func() {
            result := calc.Add(2, 3)
            
            Convey("Then the result should be correct", func() {
                So(result, ShouldEqual, 5)
            })
        })
        
        Convey("When dividing by zero", func() {
            _, err := calc.Divide(10, 0)
            
            Convey("Then an error should occur", func() {
                So(err, ShouldNotBeNil)
                So(err.Error(), ShouldContainSubstring, "zero")
            })
        })
    })
}
```

**常用断言：**
```go
So(thing, ShouldEqual, thing2)
So(thing, ShouldNotEqual, thing2)
So(thing, ShouldBeTrue)
So(thing, ShouldBeFalse)
So(thing, ShouldBeNil)
So(thing, ShouldNotBeNil)
So(thing, ShouldBeEmpty)
So(thing, ShouldContain, substring)
So(thing, ShouldBeBetween, lower, upper)
```

**启动 Web UI：**
```bash
$GOPATH/bin/goconvey
```

### 3. Ginkgo（BDD 框架）

GitHub Stars: 8,000+ | Gomega 匹配器

**安装：**
```bash
go get github.com/onsi/ginkgo/v2
go get github.com/onsi/gomega
```

**使用示例：**
```go
import (
    "testing"
    . "github.com/onsi/ginkgo/v2"
    . "github.com/onsi/gomega"
)

func TestCalculator(t *testing.T) {
    RegisterFailHandler(Fail)
    RunSpecs(t, "Calculator Suite")
}

var _ = Describe("Calculator", func() {
    var calc *Calculator
    
    BeforeEach(func() {
        calc = NewCalculator()
    })
    
    Describe("Addition", func() {
        Context("with positive numbers", func() {
            It("should return the sum", func() {
                Expect(calc.Add(2, 3)).To(Equal(5))
            })
        })
        
        Context("with negative numbers", func() {
            It("should handle correctly", func() {
                Expect(calc.Add(-2, -3)).To(Equal(-5))
            })
        })
    })
    
    Describe("Division", func() {
        It("should divide correctly", func() {
            Expect(calc.Divide(10, 2)).To(Equal(5))
        })
        
        It("should return error for division by zero", func() {
            _, err := calc.Divide(10, 0)
            Expect(err).To(HaveOccurred())
        })
    })
})
```

### 4. Godog（Cucumber BDD）

GitHub Stars: 7,000+ | Gherkin 语法

**特点：**
- 使用自然语言描述测试场景
- 适合行为驱动开发
- 支持中文等自然语言

**示例：**
```gherkin
# features/calculator.feature
Feature: Calculator
  As a user
  I want to perform calculations
  So that I can get correct results

  Scenario: Adding two numbers
    Given I have a calculator
    When I add 2 and 3
    Then the result should be 5

  Scenario: Dividing by zero
    Given I have a calculator
    When I divide 10 by 0
    Then an error should occur
```

```go
// calculator_test.go
import "github.com/cucumber/godog"

func iHaveACalculator() error {
    calc = NewCalculator()
    return nil
}

func iAddAnd(a, b int) error {
    result = calc.Add(a, b)
    return nil
}

func theResultShouldBe(expected int) error {
    if result != expected {
        return fmt.Errorf("expected %d, got %d", expected, result)
    }
    return nil
}

func InitializeScenario(ctx *godog.ScenarioContext) {
    ctx.Step(`^I have a calculator$`, iHaveACalculator)
    ctx.Step(`^I add (\d+) and (\d+)$`, iAddAnd)
    ctx.Step(`^the result should be (\d+)$`, theResultShouldBe)
}
```

## Mock 技术

### 1. 手动 Mock（纯 Go 方案）

使用接口实现手动 Mock：

```go
// repository.go
package main

type UserRepository interface {
    GetByID(id int) (*User, error)
    Save(user *User) error
}

type UserService struct {
    repo UserRepository
}
```

```go
// mock_repository.go
package main

type MockUserRepository struct {
    users map[int]*User
    err   error
}

func (m *MockUserRepository) GetByID(id int) (*User, error) {
    if m.err != nil {
        return nil, m.err
    }
    return m.users[id], nil
}

func (m *MockUserRepository) Save(user *User) error {
    if m.err != nil {
        return m.err
    }
    m.users[user.ID] = user
    return nil
}
```

```go
// user_service_test.go
package main

import "testing"

func TestUserService_GetUser(t *testing.T) {
    // 创建 mock
    mockRepo := &MockUserRepository{
        users: map[int]*User{
            1: {ID: 1, Name: "Alice"},
        },
    }
    
    // 注入 mock
    service := &UserService{repo: mockRepo}
    
    // 测试
    user, err := service.GetUser(1)
    if err != nil {
        t.Fatalf("unexpected error: %v", err)
    }
    if user.Name != "Alice" {
        t.Errorf("expected Alice, got %s", user.Name)
    }
}
```

### 2. Testify Mock

```go
import (
    "github.com/stretchr/testify/mock"
)

type MockUserRepository struct {
    mock.Mock
}

func (m *MockUserRepository) GetByID(id int) (*User, error) {
    args := m.Called(id)
    if args.Get(0) == nil {
        return nil, args.Error(1)
    }
    return args.Get(0).(*User), args.Error(1)
}

func (m *MockUserRepository) Save(user *User) error {
    args := m.Called(user)
    return args.Error(0)
}

// 测试
func TestUserService(t *testing.T) {
    mockRepo := new(MockUserRepository)
    service := &UserService{repo: mockRepo}
    
    // 设置期望
    mockRepo.On("GetByID", 1).Return(&User{ID: 1, Name: "Alice"}, nil)
    mockRepo.On("GetByID", 2).Return(nil, errors.New("not found"))
    
    // 执行测试
    user, err := service.GetUser(1)
    assert.NoError(t, err)
    assert.Equal(t, "Alice", user.Name)
    
    // 验证期望被满足
    mockRepo.AssertExpectations(t)
}
```

### 3. GoMock

**安装：**
```bash
go get go.uber.org/mock
go install go.uber.org/mock/mockgen@latest
```

**生成 Mock：**
```bash
mockgen -source=repository.go -destination=mock_repository.go -package=main
```

**使用：**
```go
import (
    "go.uber.org/mock/gomock"
)

func TestUserServiceWithGoMock(t *testing.T) {
    ctrl := gomock.NewController(t)
    defer ctrl.Finish()
    
    // 创建 mock
    mockRepo := NewMockUserRepository(ctrl)
    
    // 设置期望
    mockRepo.EXPECT().GetByID(1).Return(&User{ID: 1, Name: "Alice"}, nil)
    mockRepo.EXPECT().Save(gomock.Any()).Return(nil)
    
    // 使用 mock
    service := &UserService{repo: mockRepo}
    user, err := service.GetUser(1)
    
    assert.NoError(t, err)
    assert.Equal(t, "Alice", user.Name)
}
```

### 4. HTTP Mock（httptest）

Go 标准库提供的 HTTP 测试工具：

```go
import (
    "net/http"
    "net/http/httptest"
)

func TestAPIClient(t *testing.T) {
    // 创建 mock 服务器
    server := httptest.NewServer(http.HandlerFunc(
        func(w http.ResponseWriter, r *http.Request) {
            // 验证请求
            assert.Equal(t, "/api/users", r.URL.Path)
            assert.Equal(t, "GET", r.Method)
            
            // 返回 mock 响应
            w.Header().Set("Content-Type", "application/json")
            w.WriteHeader(http.StatusOK)
            w.Write([]byte(`{"id": 1, "name": "Alice"}`))
        },
    ))
    defer server.Close()
    
    // 使用 mock 服务器
    client := NewAPIClient(server.URL)
    user, err := client.GetUser(1)
    
    assert.NoError(t, err)
    assert.Equal(t, "Alice", user.Name)
}
```

## 测试数据库

### 使用 sqlmock

```bash
go get github.com/DATA-DOG/go-sqlmock
```

```go
import (
    "testing"
    "github.com/DATA-DOG/go-sqlmock"
)

func TestRepository(t *testing.T) {
    db, mock, err := sqlmock.New()
    if err != nil {
        t.Fatalf("an error '%s' was not expected", err)
    }
    defer db.Close()
    
    // 设置期望
    mock.ExpectQuery("SELECT id, name FROM users WHERE id = ?").
        WithArgs(1).
        WillReturnRows(sqlmock.NewRows([]string{"id", "name"}).
            AddRow(1, "Alice"))
    
    // 执行测试
    repo := NewRepository(db)
    user, err := repo.GetByID(1)
    
    assert.NoError(t, err)
    assert.Equal(t, "Alice", user.Name)
    
    // 验证所有期望都被满足
    assert.NoError(t, mock.ExpectationsWereMet())
}
```

## 测试覆盖率

### 生成覆盖率报告

```bash
# 运行测试并生成覆盖率文件
go test -coverprofile=coverage.out ./...

# 查看覆盖率摘要
go tool cover -func=coverage.out

# 生成 HTML 报告
go tool cover -html=coverage.out -o coverage.html

# 设置覆盖率阈值
go test -cover -coverprofile=coverage.out ./...
```

### 测试覆盖率最佳实践

| 建议 | 说明 |
|------|------|
| **目标覆盖率** | 一般建议达到 70-80%，核心业务代码应更高 |
| **不要追求 100%** | 覆盖率不代表质量，重点关注边界条件和错误处理 |
| **重点覆盖** | 核心业务逻辑、复杂算法、关键路径 |
| **集成测试** | 结合单元测试和集成测试，覆盖不同层次 |

## 基准测试

### 基本用法

```go
func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Add(2, 3)
    }
}

func BenchmarkAddParallel(b *testing.B) {
    b.RunParallel(func(pb *testing.PB) {
        for pb.Next() {
            Add(2, 3)
        }
    })
}
```

### 运行基准测试

```bash
# 运行基准测试
go test -bench=.

# 运行特定基准测试
go test -bench=BenchmarkAdd

# 运行 5 秒
go test -bench=. -benchtime=5s

# 内存分析
go test -bench=. -benchmem

# CPU 分析
go test -bench=. -cpuprofile=cpu.prof
go tool pprof cpu.prof
```

### 基准测试结果解读

```
BenchmarkAdd-8           1000000000      0.251 ns/op     0 B/op     0 allocs/op
```

- `BenchmarkAdd-8`：测试名称，8 表示 GOMAXPROCS
- `1000000000`：执行次数（b.N）
- `0.251 ns/op`：每次操作耗时
- `0 B/op`：每次操作分配的内存
- `0 allocs/op`：每次操作的内存分配次数

## 测试最佳实践总结

### ✅ 应该做的

1. **使用表驱动测试** - 减少重复代码，易于扩展
2. **保持测试独立** - 每个测试应该是独立的，不依赖执行顺序
3. **清晰的测试名称** - 使用描述性的测试名称和子测试
4. **测试边界条件** - 空值、零值、最大值、错误输入
5. **使用辅助函数** - 提取公共的测试设置和断言逻辑
6. **并行执行** - 适当使用 `t.Parallel()` 加速测试
7. **测试表分离** - 将测试表放在测试函数外部或单独文件
8. **使用 testdata** - 将测试数据放在 `testdata` 目录

### ❌ 不应该做的

1. **不要测试实现细节** - 测试行为而不是实现
2. **不要忽略错误** - 总是检查错误返回值
3. **不要过度使用 Mock** - 只在必要时使用 Mock
4. **不要依赖外部资源** - 使用 Mock 或测试替身隔离外部依赖
5. **不要重复测试逻辑** - 不要在测试中重复被测代码的逻辑
6. **不要忽略测试失败** - 不要跳过失败的测试或注释掉

### 测试金字塔

```
    /\
   /  \  E2E 测试（少量）
  /----\
 /      \  集成测试（适中）
/--------\
/          \  单元测试（大量）
------------
```

### 测试文件组织

```
myproject/
├── calculator.go
├── calculator_test.go      # 单元测试
├── integration_test.go     # 集成测试
├── benchmark_test.go       # 基准测试
├── example_test.go         # 示例测试
├── testdata/               # 测试数据
│   ├── input.json
│   └── expected.json
└── internal/
    ├── helper.go
    └── helper_test.go
```

## 相关资源

- [Go Testing 官方文档](https://golang.org/pkg/testing/)
- [Testify GitHub](https://github.com/stretchr/testify)
- [GoConvey 文档](https://smartystreets.github.io/goconvey/)
- [Ginkgo 文档](https://onsi.github.io/ginkgo/)
- [GoMock 文档](https://github.com/uber-go/mock)
- [Go Wiki: TableDrivenTests](https://github.com/golang/go/wiki/TableDrivenTests)
- [Dave Cheney: Prefer table driven tests](https://dave.cheney.net/2019/05/07/prefer-table-driven-tests)
