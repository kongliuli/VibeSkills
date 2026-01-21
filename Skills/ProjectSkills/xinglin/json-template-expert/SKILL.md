---
name: json-template-expert
description: "JSON模板设计和优化技能：负责JSON模板的设计规范制定、结构优化功能，并提供对应的C#数据结构调整方案及使用示例，确保JSON数据与C#对象之间的高效序列化与反序列化。"
---

# json-template-expert Skill

为项目提供专业的JSON模板设计和优化能力，确保JSON数据与C#对象之间的高效序列化与反序列化，提高数据传输效率和代码质量。

## When to Use This Skill

Trigger when any of these applies:
- 需要设计新的JSON模板结构
- 需要优化现有JSON结构以提高性能
- 需要为JSON数据设计对应的C#数据结构
- 需要解决JSON序列化与反序列化的性能问题
- 需要确保JSON数据与C#对象之间的一致性

## Not For / Boundaries

- 不负责JSON数据的业务逻辑实现
- 不替代数据库设计
- 不处理网络传输层的优化
- 不负责复杂的JSON查询操作

## Quick Reference

### Common Patterns

**Pattern 1:** JSON模板设计规范
```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "UserTemplate",
  "description": "用户信息模板",
  "type": "object",
  "properties": {
    "id": {
      "type": "integer",
      "description": "用户ID",
      "minimum": 1
    },
    "name": {
      "type": "string",
      "description": "用户名",
      "minLength": 1,
      "maxLength": 50
    },
    "email": {
      "type": "string",
      "description": "电子邮箱",
      "format": "email"
    },
    "roles": {
      "type": "array",
      "description": "用户角色列表",
      "items": {
        "type": "string",
        "enum": ["Admin", "User", "Guest"]
      }
    },
    "profile": {
      "type": "object",
      "description": "用户个人资料",
      "properties": {
        "age": {
          "type": "integer",
          "minimum": 0,
          "maximum": 150
        },
        "address": {
          "type": "string"
        }
      },
      "required": []
    }
  },
  "required": ["id", "name", "email"]
}
```

**Pattern 2:** C#数据结构设计
```csharp
// C#数据结构
using System;
using System.Collections.Generic;
using System.Text.Json.Serialization;

namespace JsonTemplates
{
    /// <summary>
    /// 用户信息类
    /// </summary>
    public class User
    {
        /// <summary>
        /// 用户ID
        /// </summary>
        [JsonPropertyName("id")]
        public int Id { get; set; }

        /// <summary>
        /// 用户名
        /// </summary>
        [JsonPropertyName("name")]
        public string Name { get; set; }

        /// <summary>
        /// 电子邮箱
        /// </summary>
        [JsonPropertyName("email")]
        public string Email { get; set; }

        /// <summary>
        /// 用户角色列表
        /// </summary>
        [JsonPropertyName("roles")]
        public List<string> Roles { get; set; } = new List<string>();

        /// <summary>
        /// 用户个人资料
        /// </summary>
        [JsonPropertyName("profile")]
        public Profile Profile { get; set; } = new Profile();
    }

    /// <summary>
    /// 用户个人资料类
    /// </summary>
    public class Profile
    {
        /// <summary>
        /// 年龄
        /// </summary>
        [JsonPropertyName("age")]
        public int? Age { get; set; }

        /// <summary>
        /// 地址
        /// </summary>
        [JsonPropertyName("address")]
        public string Address { get; set; }
    }
}
```

**Pattern 3:** JSON结构优化
```json
// 优化前
{
  "user": {
    "id": 1,
    "name": "John Doe",
    "contact": {
      "email": "john@example.com",
      "phone": "1234567890"
    },
    "orders": [
      {
        "id": 101,
        "items": [
          {
            "product": {
              "id": 1,
              "name": "Product 1"
            },
            "quantity": 2
          }
        ]
      }
    ]
  }
}

// 优化后
{
  "userId": 1,
  "userName": "John Doe",
  "email": "john@example.com",
  "phone": "1234567890",
  "orders": [
    {
      "orderId": 101,
      "items": [
        {
          "productId": 1,
          "productName": "Product 1",
          "quantity": 2
        }
      ]
    }
  ]
}
```

**Pattern 4:** 序列化与反序列化优化
```csharp
// 序列化与反序列化优化
using System;
using System.Text.Json;
using System.Text.Json.Serialization;

namespace JsonSerialization
{
    public class JsonSerializerHelper
    {
        /// <summary>
        /// 优化的JSON序列化选项
        /// </summary>
        public static JsonSerializerOptions DefaultOptions {
            get {
                return new JsonSerializerOptions
                {
                    PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
                    IgnoreNullValues = true,
                    WriteIndented = false, // 生产环境使用false提高性能
                    Converters = {
                        new JsonStringEnumConverter(),
                        new DateTimeConverter()
                    }
                };
            }
        }

        /// <summary>
        /// 序列化对象为JSON字符串
        /// </summary>
        /// <typeparam name="T">对象类型</typeparam>
        /// <param name="obj">要序列化的对象</param>
        /// <returns>JSON字符串</returns>
        public static string Serialize<T>(T obj)
        {
            return JsonSerializer.Serialize(obj, DefaultOptions);
        }

        /// <summary>
        /// 反序列化JSON字符串为对象
        /// </summary>
        /// <typeparam name="T">对象类型</typeparam>
        /// <param name="json">JSON字符串</param>
        /// <returns>反序列化后的对象</returns>
        public static T Deserialize<T>(string json)
        {
            return JsonSerializer.Deserialize<T>(json, DefaultOptions);
        }
    }

    /// <summary>
    /// 日期时间转换器
    /// </summary>
    public class DateTimeConverter : JsonConverter<DateTime>
    {
        public override DateTime Read(ref Utf8JsonReader reader, Type typeToConvert, JsonSerializerOptions options)
        {
            return DateTime.Parse(reader.GetString());
        }

        public override void Write(Utf8JsonWriter writer, DateTime value, JsonSerializerOptions options)
        {
            writer.WriteStringValue(value.ToString("yyyy-MM-ddTHH:mm:ss.fffZ"));
        }
    }
}
```

**Pattern 5:** 性能优化策略
```csharp
// 性能优化策略
using System;
using System.Text.Json;
using System.Buffers;
using System.IO;

namespace JsonPerformance
{
    public class JsonPerformanceOptimizer
    {
        /// <summary>
        /// 高性能序列化
        /// </summary>
        /// <typeparam name="T">对象类型</typeparam>
        /// <param name="obj">要序列化的对象</param>
        /// <returns>JSON字符串</returns>
        public static string HighPerformanceSerialize<T>(T obj)
        {
            // 使用池化的内存流减少GC压力
            using var stream = new MemoryStream();
            using var writer = new Utf8JsonWriter(stream, new JsonWriterOptions
            {
                SkipValidation = true, // 跳过验证提高性能
                Indented = false
            });

            JsonSerializer.Serialize(writer, obj);
            writer.Flush();
            
            return System.Text.Encoding.UTF8.GetString(stream.ToArray());
        }

        /// <summary>
        /// 高性能反序列化
        /// </summary>
        /// <typeparam name="T">对象类型</typeparam>
        /// <param name="json">JSON字符串</param>
        /// <returns>反序列化后的对象</returns>
        public static T HighPerformanceDeserialize<T>(string json)
        {
            // 使用ReadOnlySpan减少内存分配
            var jsonSpan = json.AsSpan();
            return JsonSerializer.Deserialize<T>(jsonSpan);
        }

        /// <summary>
        /// 批量处理JSON数据
        /// </summary>
        /// <typeparam name="T">对象类型</typeparam>
        /// <param name="jsonArray">JSON数组字符串</param>
        /// <returns>对象列表</returns>
        public static T[] BatchProcess<T>(string jsonArray)
        {
            // 直接反序列化为数组以提高性能
            return JsonSerializer.Deserialize<T[]>(jsonArray);
        }
    }
}
```

## Examples

### Example 1: 完整的JSON模板与C#数据结构

**Scenario:** 设计用户信息模板并创建对应的C#数据结构

**Steps:**
1. 设计JSON模板结构
2. 创建对应的C#数据结构
3. 实现序列化与反序列化
4. 测试数据一致性

**Code:**
```csharp
// JSON模板
/*
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "roles": ["Admin", "User"],
  "profile": {
    "age": 30,
    "address": "123 Main St"
  }
}
*/

// C#数据结构
public class User
{
    [JsonPropertyName("id")]
    public int Id { get; set; }

    [JsonPropertyName("name")]
    public string Name { get; set; }

    [JsonPropertyName("email")]
    public string Email { get; set; }

    [JsonPropertyName("roles")]
    public List<string> Roles { get; set; } = new List<string>();

    [JsonPropertyName("profile")]
    public Profile Profile { get; set; } = new Profile();
}

public class Profile
{
    [JsonPropertyName("age")]
    public int? Age { get; set; }

    [JsonPropertyName("address")]
    public string Address { get; set; }
}

// 使用示例
public class Program
{
    public static void Main()
    {
        // 创建用户对象
        var user = new User
        {
            Id = 1,
            Name = "John Doe",
            Email = "john@example.com",
            Roles = new List<string> { "Admin", "User" },
            Profile = new Profile
            {
                Age = 30,
                Address = "123 Main St"
            }
        };

        // 序列化
        string json = JsonSerializerHelper.Serialize(user);
        Console.WriteLine("Serialized JSON:");
        Console.WriteLine(json);

        // 反序列化
        User deserializedUser = JsonSerializerHelper.Deserialize<User>(json);
        Console.WriteLine("\nDeserialized User:");
        Console.WriteLine($"Id: {deserializedUser.Id}");
        Console.WriteLine($"Name: {deserializedUser.Name}");
        Console.WriteLine($"Email: {deserializedUser.Email}");
        Console.WriteLine($"Roles: {string.Join(", ", deserializedUser.Roles)}");
        Console.WriteLine($"Age: {deserializedUser.Profile.Age}");
        Console.WriteLine($"Address: {deserializedUser.Profile.Address}");
    }
}
```

**Expected Output:**
- 序列化后的JSON字符串
- 反序列化后的用户对象
- 验证数据一致性

### Example 2: 复杂嵌套结构的优化

**Scenario:** 优化复杂的订单数据结构

**Steps:**
1. 分析原始JSON结构
2. 优化JSON结构
3. 调整C#数据结构
4. 测试性能提升

**Code:**
```csharp
// 原始JSON结构
/*
{
  "order": {
    "id": 1,
    "customer": {
      "id": 101,
      "name": "John Doe",
      "address": {
        "street": "123 Main St",
        "city": "New York"
      }
    },
    "items": [
      {
        "product": {
          "id": 201,
          "name": "Product 1",
          "price": 19.99
        },
        "quantity": 2
      },
      {
        "product": {
          "id": 202,
          "name": "Product 2",
          "price": 9.99
        },
        "quantity": 1
      }
    ],
    "total": 49.97,
    "date": "2023-01-01T12:00:00"
  }
}
*/

// 优化后的JSON结构
/*
{
  "orderId": 1,
  "customerId": 101,
  "customerName": "John Doe",
  "customerStreet": "123 Main St",
  "customerCity": "New York",
  "items": [
    {
      "productId": 201,
      "productName": "Product 1",
      "price": 19.99,
      "quantity": 2
    },
    {
      "productId": 202,
      "productName": "Product 2",
      "price": 9.99,
      "quantity": 1
    }
  ],
  "total": 49.97,
  "orderDate": "2023-01-01T12:00:00"
}
*/

// 优化后的C#数据结构
public class OptimizedOrder
{
    [JsonPropertyName("orderId")]
    public int OrderId { get; set; }

    [JsonPropertyName("customerId")]
    public int CustomerId { get; set; }

    [JsonPropertyName("customerName")]
    public string CustomerName { get; set; }

    [JsonPropertyName("customerStreet")]
    public string CustomerStreet { get; set; }

    [JsonPropertyName("customerCity")]
    public string CustomerCity { get; set; }

    [JsonPropertyName("items")]
    public List<OrderItem> Items { get; set; } = new List<OrderItem>();

    [JsonPropertyName("total")]
    public decimal Total { get; set; }

    [JsonPropertyName("orderDate")]
    public DateTime OrderDate { get; set; }
}

public class OrderItem
{
    [JsonPropertyName("productId")]
    public int ProductId { get; set; }

    [JsonPropertyName("productName")]
    public string ProductName { get; set; }

    [JsonPropertyName("price")]
    public decimal Price { get; set; }

    [JsonPropertyName("quantity")]
    public int Quantity { get; set; }
}

// 性能测试
public class PerformanceTest
{
    public static void TestSerializationPerformance()
    {
        // 创建测试数据
        var order = new OptimizedOrder
        {
            OrderId = 1,
            CustomerId = 101,
            CustomerName = "John Doe",
            CustomerStreet = "123 Main St",
            CustomerCity = "New York",
            Items = new List<OrderItem>
            {
                new OrderItem { ProductId = 201, ProductName = "Product 1", Price = 19.99M, Quantity = 2 },
                new OrderItem { ProductId = 202, ProductName = "Product 2", Price = 9.99M, Quantity = 1 }
            },
            Total = 49.97M,
            OrderDate = DateTime.Now
        };

        // 测试序列化性能
        var stopwatch = System.Diagnostics.Stopwatch.StartNew();
        for (int i = 0; i < 10000; i++)
        {
            var json = JsonSerializerHelper.Serialize(order);
        }
        stopwatch.Stop();
        Console.WriteLine($"Serialization time: {stopwatch.ElapsedMilliseconds}ms");

        // 测试反序列化性能
        var jsonString = JsonSerializerHelper.Serialize(order);
        stopwatch.Restart();
        for (int i = 0; i < 10000; i++)
        {
            var deserialized = JsonSerializerHelper.Deserialize<OptimizedOrder>(jsonString);
        }
        stopwatch.Stop();
        Console.WriteLine($"Deserialization time: {stopwatch.ElapsedMilliseconds}ms");
    }
}
```

**Expected Output:**
- 序列化性能测试结果
- 反序列化性能测试结果
- 验证优化效果

## References

- `references/json-schema.md`: JSON Schema设计指南
- `references/csharp-models.md`: C#数据结构设计指南
- `references/serialization.md`: 序列化与反序列化指南
- `references/performance.md`: 性能优化指南
- `references/best-practices.md`: 最佳实践指南

## Maintenance

- Sources: JSON Schema Specification, System.Text.Json Documentation, C# Documentation
- Last updated: 2026-01-21
- Known limits: 不处理极端复杂的嵌套结构