

# การกำหนด scopes

ทำการรันคำสั่งใน Terminal เพื่อกำหนด scope สำหรับ Workshop นี้

```
dotnet user-secrets set scopes "User.Read;Directory.ReadWrite.All;Group.ReadWrite.All;Team.Create;Channel.Create;TeamsTab.Create;TeamsTab.ReadWriteForTeam;TeamsTab.ReadWrite.All"
```