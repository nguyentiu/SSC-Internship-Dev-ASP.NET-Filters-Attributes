# SSC-Internship-Dev-ASP.NET-Filters-Attributes
# Hướng Dẫn Về Filters & Attributes Trong ASP.NET Core
## Giới Thiệu
Filters và Attributes là những công cụ mạnh mẽ trong ASP.NET Core, giúp bạn kiểm soát luồng xử lý yêu cầu và thực hiện các tác vụ cụ thể trước hoặc sau khi controller action được gọi. Chúng cho phép bạn tách rời logic chung ra khỏi controller và tái sử dụng lại ở nhiều nơi khác nhau trong ứng dụng.

## 1. Filters Là Gì?
Filters là các thành phần trong ASP.NET Core cho phép bạn can thiệp vào quá trình xử lý của request hoặc response. Chúng được thực thi tại các điểm cụ thể trong pipeline xử lý của ứng dụng và có thể được áp dụng trên toàn bộ ứng dụng, cho một controller cụ thể, hoặc chỉ cho một action.

ASP.NET Core hỗ trợ năm loại filters chính:

- Authorization Filters: Được sử dụng để xác định xem một người dùng có được phép truy cập một tài nguyên cụ thể hay không.
- Resource Filters: Xử lý các yêu cầu trước và sau khi các filter khác hoặc actions được thực thi.
- Action Filters: Xử lý logic trước và sau khi một action của controller được thực thi.
- Exception Filters: Xử lý các lỗi không mong muốn xảy ra trong quá trình xử lý request.
- Result Filters: Xử lý logic trước và sau khi kết quả của một action được trả về cho client.
## 2. Các Loại Filters
### 2.1 Authorization Filters

Authorization Filters xác định liệu người dùng hiện tại có quyền truy cập vào tài nguyên yêu cầu hay không. Chúng thường được sử dụng để thực hiện kiểm tra xác thực và phân quyền.

```csharp
[Authorize]
public class AdminController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}
```
Trong ví dụ trên, `[Authorize]` là một Authorization Filter được áp dụng trên `AdminController`, đảm bảo rằng chỉ những người dùng đã xác thực mới có thể truy cập vào controller này.

### 2.2 Resource Filters

Resource Filters chạy trước khi các filters khác và có thể thay đổi hoặc rút ngắn pipeline. Chúng cũng có thể xử lý cache hoặc thay đổi yêu cầu.

```csharp
public class SampleResourceFilter : IResourceFilter
{
    public void OnResourceExecuting(ResourceExecutingContext context)
    {
        // Thực hiện trước khi action được gọi
    }

    public void OnResourceExecuted(ResourceExecutedContext context)
    {
        // Thực hiện sau khi action hoàn thành
    }
}
```
### 2.3 Action Filters

Action Filters cho phép bạn chạy mã trước và sau khi một action của controller được thực thi. Chúng rất hữu ích cho các nhiệm vụ như logging, xử lý yêu cầu, hoặc xác thực đầu vào.

```csharp
public class LogActionFilter : IActionFilter
{
    public void OnActionExecuting(ActionExecutingContext context)
    {
        Console.WriteLine("Action đang được thực thi.");
    }

    public void OnActionExecuted(ActionExecutedContext context)
    {
        Console.WriteLine("Action đã hoàn thành.");
    }
}
```
### 2.4 Exception Filters

Exception Filters được sử dụng để xử lý các ngoại lệ không được xử lý xảy ra trong quá trình xử lý request. Chúng thường được sử dụng để log lỗi hoặc trả về các thông báo lỗi tùy chỉnh cho client.

```csharp
public class CustomExceptionFilter : IExceptionFilter
{
    public void OnException(ExceptionContext context)
    {
        // Xử lý ngoại lệ
        context.Result = new ContentResult
        {
            Content = "An error occurred."
        };
    }
}
```
### 2.5 Result Filters

Result Filters chạy mã trước và sau khi một action result (như ViewResult, JsonResult) được thực thi và có thể sửa đổi kết quả cuối cùng trước khi nó được trả về cho client.

```csharp
public class ResultLogFilter : IResultFilter
{
    public void OnResultExecuting(ResultExecutingContext context)
    {
        Console.WriteLine("Trước khi result được trả về.");
    }

    public void OnResultExecuted(ResultExecutedContext context)
    {
        Console.WriteLine("Result đã được trả về.");
    }
}
```
## 3. Cách Áp Dụng Filters
Filters có thể được áp dụng theo nhiều cách khác nhau:

- Toàn bộ ứng dụng: Áp dụng trên toàn bộ ứng dụng trong `Startup.cs`.
- Controller: Áp dụng cho một controller cụ thể.
- Action: Áp dụng cho một action cụ thể trong controller.
Áp Dụng Filters Toàn Bộ Ứng Dụng

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers(config =>
    {
        config.Filters.Add(new LogActionFilter());
    });
}
```
Áp Dụng Filters Trên Một Controller

```csharp
[ServiceFilter(typeof(LogActionFilter))]
public class HomeController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}
```
Áp Dụng Filters Trên Một Action

```csharp
public class HomeController : Controller
{
    [ServiceFilter(typeof(LogActionFilter))]
    public IActionResult Index()
    {
        return View();
    }
}
```
## 4. Tạo Custom Filters
Bạn có thể tạo custom filters bằng cách implement các interface tương ứng như `IActionFilter`, `IResourceFilter`, `IExceptionFilter`, `IResultFilter`, hoặc `IAuthorizationFilter`.

Ví Dụ: Tạo Custom Action Filter

```csharp
public class LogActionFilter : IActionFilter
{
    public void OnActionExecuting(ActionExecutingContext context)
    {
        Console.WriteLine("Action đang được thực thi.");
    }

    public void OnActionExecuted(ActionExecutedContext context)
    {
        Console.WriteLine("Action đã hoàn thành.");
    }
}
```
Sau đó, bạn có thể đăng ký filter này trong `Startup.cs` hoặc áp dụng nó trực tiếp trên controller hoặc action.

## 5. Tổng kết
Filters và Attributes là những công cụ rất hữu ích trong ASP.NET Core để tách rời và tái sử dụng logic xử lý chung. Chúng giúp bạn kiểm soát tốt hơn luồng xử lý yêu cầu và phản hồi trong ứng dụng, giúp mã của bạn sạch sẽ và dễ bảo trì hơn.
