## Fysite Backend

### Project structure
```
project/
├── app
├── config
├── model
	├── domain
	└── web
├── helper
├── middleware
├── controller
├── service
└── repository
```

### .env.example
```.env
DB_DSN=
APP_PORT=
APP_URL=

FRONTEND_URL=
COOKIE_DOMAIN=
ENVIRONMENT=development

ACCESS_TOKEN_SECRET=
REFRESH_TOKEN_SECRET=

GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=

REDIS_ADDR=
```

### Middleware structure
```
Frontend -> Middleware (Auth & Request ID) -> MVC Logics
```

### Auth structure
```
/auth/google: generate rand state -> save to redis (e.g 5 mins) -> redirect
/auth/google/callback: get & verify state in redis -> get code -> exchange to user data -> save db -> set cookie (access & refresh token) -> redirect to frontend
```

### API design pattern
```
data: {},
meta: {
	success: boolean
	message: string
	error_code: CONSTANT_VAR
	request_id: RAND_STRING
}
```

### Database model
```
type User struct {
	ID  uint  `gorm:"primaryKey"`
	GoogleID  string  `gorm:"unique;not null"`
	Email  string  `gorm:"unique;not null"`
	Name  string  `gorm:"size:100"`
	Username  string  `gorm:"size:50;unique"`
	AvatarURL  string  `gorm:"size:255"`
	
	Assets []Asset  `gorm:"foreignKey:UserID"`

	CreatedAt  time.Time
	UpdatedAt  time.Time
	DeletedAt  gorm.DeletedAt  `gorm:"index"`
}

type Asset struct {
	ID  uint  `gorm:"primaryKey"`
	Title  string  `gorm:"size:50;not null"`
	Description  string  `gorm:"size:255;not null"`
	
	UserID  uint  `gorm:"not null;index"`
	User  User  `gorm:"foreignKey:UserID"`

	FileID  uint  `json:"file_id" gorm:"index"`
	File  Upload  `json:"file" gorm:"foreignKey:FileID"`

	ImageID  uint  `json:"image_id" gorm:"index"`
	Image  Upload  `json:"file" gorm:"foreignKey:ImageID"`

	CreatedAt  time.Time
	UpdatedAt  time.Time
	DeletedAt  gorm.DeletedAt  `gorm:"index"`
}

type Upload struct {
	ID  uint  `json:"id" gorm:"primaryKey"`
	FilePath  string  `json:"file_path" gorm:"not null"`

	CreatedAt  time.Time  `json:"created_at"`
	UpdatedAt  time.Time  `json:"updated_at"`
	DeletedAt  gorm.DeletedAt  `gorm:"index"`
}
```

## Todo list

- [ ] Implement CRUD of asset model 
- [ ] Add Google OAuth2 authentication feature
- [ ] Add middleware & verify data ownership
- [ ] Implement upload functionality
