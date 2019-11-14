
# สร้างกลไก Jason Web Token

## 1. ติดตั้ง package

รันคำสั่งติดตั้ง package 

```bash
dotnet add package System.IdentityModel.Tokens.Jwt --version 5.6.0
```

## 2. สร้าง Class ในการส่ง JSON กลับไปให้ Client 

สร้างไฟล์ `Models/ResultToken.cs`

```cs
namespace UserAPI.Models
{
    public class ResultToken
    {
        public string Token { get; set; }

    }
} 
```

## 3. สร้าง token และส่งกลับให้ client ถ้าพบว่ามี user อยู่ในระบบ

เริ่มจาก using namespace ที่จำเป็น

```cs
using System;
using System.Collections.Generic;
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Tokens;
using UserAPI.Models;
using UserAPI.Services;
```

จากนั้นให้เรียกใช้คำสั่งสร้าง token ใน Route `/login`

```cs
[HttpPost("login")]
public ActionResult<ResultToken> Login(User user)
{
    if (_userService.isExist(user.Username, user.Password))
    {

        var tokenHandler = new JwtSecurityTokenHandler();
        var key = Encoding.ASCII.GetBytes("secret_word_is_nextflow_and_that_s_it");
        var tokenDescriptor = new SecurityTokenDescriptor
        {
            Subject = new ClaimsIdentity(new Claim[]
        {
            new Claim(ClaimTypes.UserData, user.Username)
        }),
            Expires = DateTime.UtcNow.AddDays(7),
            SigningCredentials = new SigningCredentials(new SymmetricSecurityKey(key), SecurityAlgorithms.HmacSha256Signature)
        };
        var token = tokenHandler.CreateToken(tokenDescriptor);
        var resultToken = tokenHandler.WriteToken(token);

        // ส่ง 200 พร้อมด้วย json ที่มี token กลับไปให้ client
        return Ok(new ResultToken{ Token = resultToken });
    }
    
    // ส่ง 404 ถ้าไม่พบ user ในระบบ
    return NotFound();
}
```

## A. ไฟล์เต็ม 

```cs
using System;
using System.Collections.Generic;
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Tokens;
using UserAPI.Models;
using UserAPI.Services;

namespace UserAPI.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class UsersController : ControllerBase
    {
        private readonly UserService _userService;

        public UsersController(UserService userService)
        {
            _userService = userService;
        }

        [HttpGet]
        public ActionResult<List<User>> Get()
        {
            return _userService.Get();
        }

        [HttpPost("login")]
        public ActionResult<ResultToken> Login(User user)
        {
            if (_userService.isExist(user.Username, user.Password))
            {

                var tokenHandler = new JwtSecurityTokenHandler();
                var key = Encoding.ASCII.GetBytes("secret_word_is_nextflow_and_that_s_it");
                var tokenDescriptor = new SecurityTokenDescriptor
                {
                    Subject = new ClaimsIdentity(new Claim[]
                {
                    new Claim(ClaimTypes.UserData, user.Username)
                }),
                    Expires = DateTime.UtcNow.AddDays(7),
                    SigningCredentials = new SigningCredentials(new SymmetricSecurityKey(key), SecurityAlgorithms.HmacSha256Signature)
                };
                var token = tokenHandler.CreateToken(tokenDescriptor);
                var resultToken = tokenHandler.WriteToken(token);

                return Ok(new ResultToken{ Token = resultToken });
            }

            return NotFound();
        }

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<User> Get(string id)
        {
            var user = _userService.Get(id);

            if (user == null)
            {
                return NotFound();
            }

            return user;
        }

        [HttpPost]
        public ActionResult<User> Create(User user)
        {
            _userService.Create(user);

            return CreatedAtRoute("GetBook", new { id = user.Id.ToString() }, user);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, User userIn)
        {
            var user = _userService.Get(id);

            if (user == null)
            {
                return NotFound();
            }

            _userService.Update(id, userIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var user = _userService.Get(id);

            if (user == null)
            {
                return NotFound();
            }

            _userService.Remove(user.Id);

            return NoContent();
        }
    }
}
```