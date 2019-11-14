
# ติดตั้ง CORS

เราสามารถเปิดการทำงานของ CORS เพื่อกำหนดใช้ Application ภายนอกส่ง request มาที่ Web API ของเราได้

เปิดไฟล์ `Startup.cs` 

เราจะเพิ่มโค้ดลงไปใน method `ConfigureServices(IServiceCollection services)`

```cs
public void ConfigureServices(IServiceCollection services)
{
    
    services.AddCors(options => 
        // สร้างกฏของ CORS ขึ้นมา และตั้งชื่อว่า AllowAll
        options.AddPolicy("AllowAll", builder => {
            builder.AllowAnyOrigin()
            .AllowAnyMethod()
            .AllowAnyHeader();
    }));

    //...

}
```

และไน method `Configure(IApplicationBuilder app, IWebHostEnvironment env)`

```cs
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            // เพิ่มชื่อของ Cors ที่สร้างไว้ ลงไปใน App
            app.UseCors("AllowAll");

            app.UseHttpsRedirection();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllers();
            });
        }
```