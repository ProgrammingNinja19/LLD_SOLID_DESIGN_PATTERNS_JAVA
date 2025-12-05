Sure! Let's break this down in **plain English** first, then show you the **Java code**.

### What is the Chain of Responsibility Pattern?

Imagine you have a chain of people who can handle a request:
- The request comes in → Person A looks at it.
- If Person A can handle it → done.
- If not → passes it to Person B.
- Person B does the same → passes to C if needed, and so on.

This is exactly what the **Chain of Responsibility** design pattern is: a chain of handlers, each deciding whether to process the request or pass it to the next one.

### How Servlet Filters Use This Pattern

In Java web applications (Servlets), **Filters** are the perfect real-world example of the **Chain of Responsibility** pattern.

Here’s how it works:

1. You can define multiple filters (like logging, authentication, compression, etc.).
2. You configure them in a specific **order** in `web.xml` or using annotations.
3. When an HTTP request comes in:
   - The servlet container sends the request through **Filter 1 → Filter 2 → Filter 3 → ... → finally the Servlet**.
4. Each filter gets a `FilterChain` object.
5. Inside the filter, you do your job (e.g. check if user is logged in).
6. Then you **must** call `chain.doFilter(request, response)` to pass control to the **next filter** in the chain.
7. If you **don’t** call `chain.doFilter(...)`, the request **stops** there (useful for blocking unauthorized users).

This is **exactly** Chain of Responsibility!

### Visual Flow

```
Request → [Filter1] → [Filter2] → [Filter3] → [Your Servlet] → Response
                ↑          ↑          ↑
           call doFilter   call doFilter   call doFilter
```

### Java Code Example

Here are 3 filters + 1 servlet in the chain:

#### 1. Logging Filter
```java
public class LoggingFilter implements Filter {
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        
        System.out.println("LoggingFilter: Request received at " + new Date());
        
        // Pass to next filter in chain
        chain.doFilter(request, response);
        
        System.out.println("LoggingFilter: Response sent");
    }
    
    public void init(FilterConfig filterConfig) {}
    public void destroy() {}
}
```

#### 2. Authentication Filter
```java
public class AuthFilter implements Filter {
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        
        HttpServletRequest req = (HttpServletRequest) request;
        HttpSession session = req.getSession(false);
        
        if (session == null || session.getAttribute("user") == null) {
            HttpServletResponse res = (HttpServletResponse) response;
            res.sendRedirect("login.html");  // Block access!
            return; // ← Stops the chain here!
        }
        
        System.out.println("AuthFilter: User is authenticated");
        
        // Continue to next filter
        chain.doFilter(request, response);
    }
    
    public void init(FilterConfig filterConfig) {}
    public void destroy() {}
}
```

#### 3. Encoding Filter
```java
public class EncodingFilter implements Filter {
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        
        request.setCharacterEncoding("UTF-8");
        System.out.println("EncodingFilter: Set encoding to UTF-8");
        
        chain.doFilter(request, response);  // Always pass forward
    }
    
    public void init(FilterConfig filterConfig) {}
    public void destroy() {}
}
```

#### Final Servlet (the actual endpoint)
```java
public class HelloServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        response.setContentType("text/html");
        response.getWriter().println("<h1>Hello! You passed all filters!</h1>");
    }
}
```

#### Configure the chain (in web.xml)
```xml
<filter-mapping>
    <filter-name>LoggingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>

<filter-mapping>
    <filter-name>EncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>

<filter-mapping>
    <filter-name>AuthFilter</filter-name>
    <url-pattern>/secret</url-pattern>
</filter-mapping>
```

### Summary: Why This Is Chain of Responsibility

| Feature                        | In Servlet Filters                     |
|--------------------------------|----------------------------------------|
| Chain of handlers              | Multiple filters                       |
| Each handler decides to process or pass | `chain.doFilter()` or block            |
| Order matters                  | Defined in web.xml or @WebFilter order |
| Can stop the chain             | By not calling `chain.doFilter()`     |
| Decoupled handlers             | Filters don't know each other          |

**Servlet Filters = Real-life Chain of Responsibility Pattern!**

That’s one of the cleanest examples in all of Java EE/Servlet technology. Super useful and widely used! 🚀
