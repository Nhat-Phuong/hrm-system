# HRM Suite - Mobile + Admin + Backend

> Hệ thống quản lý nhân sự (HRM) gồm mobile app, admin dashboard và backend API. Tập trung vào chấm công, nghỉ phép, payroll và thông báo thời gian thực.

---

## 1) Tổng quan

- Mobile app cho nhân viên (Expo + React Native)
- Admin dashboard cho quản trị (React + Vite + TypeScript)
- Backend REST API (Node.js + Express + MongoDB) + Socket.IO realtime + Swagger UI

---

## 2) Kiến trúc tổng quan

```
Mobile App (Expo) -----\
Admin Dashboard ------- > Backend API (Express + MongoDB) -> Swagger UI
                         -> Socket.IO (realtime notifications)
```

---

## 3) Chức năng chính

- Đăng nhập/đăng ký, role-based access (SUPER_ADMIN, EMPLOYEE)
- Chấm công (clock-in/clock-out), QR token, geofence, thống kê trễ/absent
- Lịch làm việc (default schedule + daily override), log thay đổi
- Nghỉ phép: tạo, duyệt/từ chối, đồng bộ attendance
- Payroll: auto-calc, tạo hàng loạt, revise payroll, formula settings
- Reward/Discipline: quy tắc thưởng, kỷ luật, auto-calc reward theo attendance
- Thông báo realtime (Socket.IO), quản lý đã đọc/chưa đọc
- Quản lý phòng ban, chức vụ, nhân viên

---

## 4) Công nghệ sử dụng

**Backend**
- Node.js, Express, MongoDB
- JWT auth
- Swagger UI
- Socket.IO (thông báo thời gian thực)

**Mobile (Frontend/)**
- Expo, React Native, TypeScript
- Expo Router, NativeWind
- Socket.IO client

**Admin (adminSide/)**
- React + Vite + TypeScript
- Tailwind CSS

---

## 5) Cấu trúc thư mục

```
2025-2026-DACN/
|-- Backend/               # REST API + Socket.IO + scripts
|-- Frontend/              # Mobile app (Expo)
|-- adminSide/             # Admin dashboard (Vite + React)
|-- app/                   # Expo Router app (root)
|-- run-dev.ps1            # Chạy nhanh Backend + Frontend
|-- Images_readme/         # Ảnh demo giao diện
```

---

## 6) Yêu cầu môi trường

- Node.js >= 18
- npm
- MongoDB (local hoặc Atlas)

---

## 7) Cài đặt và chạy local

### 7.1 Backend

```bash
cd Backend
npm install
npm run dev
```

Thiết lập biến môi trường theo định dạng .env (gợi ý):

```env
PORT=3000
NODE_ENV=development
MONGODB_URI=mongodb://localhost:27017/DACN
JWT_SECRET=your-super-secret-jwt-key-change-this
JWT_EXPIRES_IN=7d
CORS_ORIGIN=http://localhost:8081

# QR / geofence
QR_SECRET=your-qr-secret
QR_WINDOW_SECONDS=10
QR_MAX_SKEW_WINDOWS=2
OFFICE_LAT=
OFFICE_LNG=
OFFICE_RADIUS_METERS=100
```

### 7.2 Mobile app (Frontend)

```bash
cd Frontend
npm install
npm start
```

Thiết lập biến môi trường theo định dạng .env:

```env
EXPO_PUBLIC_API_URL=http://localhost:3000/api
```

### 7.3 Admin dashboard

```bash
cd adminSide
npm install
npm run dev
```

Thiết lập biến môi trường theo định dạng .env:

```env
VITE_API_URL=http://localhost:3000/api
```

### 7.4 Chạy nhanh (Windows)

```powershell
.\run-dev.ps1
```

Script sẽ mở 2 terminal:
- Backend (npm start)
- Frontend (npx expo start -c)

---

## 8) Tài khoản admin mặc định

```bash
cd Backend
npm run create-admin
```

Giá trị mặc định (có thể override bằng env):
- Email: admin@example.com
- Password: admin123

---

## 9) API Docs

- Base URL: http://localhost:3000/api
- Swagger UI: http://localhost:3000/api-docs
- Health check: http://localhost:3000/health

---

## 10) Danh sách API endpoints (tóm tắt)

### Auth
- POST /api/auth/login - Đăng nhập
- POST /api/auth/signup - Đăng ký tài khoản mới
- GET /api/auth/me - Lấy thông tin người dùng hiện tại

### Attendance
- POST /api/attendance/clock-in - Chấm công vào ca
- POST /api/attendance/clock-out - Chấm công ra ca
- GET /api/attendance/current?employeeId=... - Chấm công hôm nay
- GET /api/attendance/history?employeeId=...&startDate=YYYY-MM-DD&endDate=YYYY-MM-DD&limit=30 - Lịch sử chấm công

### Employees (self-service)
- GET /api/employees/profile - Lấy hồ sơ nhân viên hiện tại
- PUT /api/employees/profile - Cập nhật hồ sơ nhân viên
- GET /api/employees/schedules/my?weekStart=YYYY-MM-DD - Lịch làm việc tuần của tôi
- POST /api/employees/:employeeId/leave-requests - Tạo yêu cầu nghỉ phép
- GET /api/employees/:employeeId/leave-requests - Danh sách yêu cầu nghỉ phép
- POST /api/employees/:employeeId/attendance-adjustments - Tạo yêu cầu điều chỉnh chấm công
- GET /api/employees/:employeeId/attendance-adjustments - Danh sách điều chỉnh chấm công
- GET /api/employees/:employeeId/payslips?year=YYYY&month=MM - Danh sách phiếu lương đã duyệt
- GET /api/employees/:employeeId/qr-code - Lấy QR token
- POST /api/employees/verify-qr - Xác thực QR token
- GET /api/employees/:id - Chi tiết nhân viên

### Notifications
- POST /api/notifications/send - Gửi thông báo (admin)
- GET /api/notifications?page=1&limit=20&unreadOnly=true|false - Danh sách thông báo của tôi
- GET /api/notifications/unread-count - Số thông báo chưa đọc
- PUT /api/notifications/:id/read - Đánh dấu đã đọc
- PUT /api/notifications/read-all - Đánh dấu đã đọc tất cả
- DELETE /api/notifications/:id - Xóa thông báo của tôi
- GET /api/notifications/sent - Danh sách thông báo đã gửi (admin)

### Schedules (shared)
- GET /api/schedules?employeeId=... - Danh sách lịch làm việc
- POST /api/schedules - Tạo lịch làm việc
- PUT /api/schedules/:id - Cập nhật lịch làm việc
- GET /api/schedules/logs?employeeId=...&limit=50 - Lịch sử thay đổi lịch

### Payrolls
- GET /api/payrolls/my?month=MM&year=YYYY - Phiếu lương của tôi
- GET /api/payrolls?month=MM&year=YYYY&employeeId=...&status=PENDING|APPROVED - Danh sách payroll (admin)
- GET /api/payrolls/employees - Danh sách nhân viên để tạo payroll
- POST /api/payrolls/auto-calculate - Tính gợi ý payroll theo tháng
- POST /api/payrolls - Tạo payroll
- POST /api/payrolls/bulk - Tạo payroll hàng loạt
- PUT /api/payrolls/:id - Cập nhật payroll
- POST /api/payrolls/:id/revise - Tạo phiên bản điều chỉnh
- DELETE /api/payrolls/bulk-delete - Xóa payroll hàng loạt

### Admin
- GET /api/admin/test - Endpoint test
- GET /api/admin/dashboard - Dữ liệu tổng hợp dashboard
- GET /api/admin/employees - Danh sách nhân viên
- POST /api/admin/employees - Tạo nhân viên + tài khoản
- PUT /api/admin/employees/:id - Cập nhật nhân viên
- DELETE /api/admin/employees/:id - Xóa nhân viên
- GET /api/admin/attendance?date=YYYY-MM-DD - Chấm công theo ngày
- GET /api/admin/attendance/today - Chấm công hôm nay
- GET /api/admin/leave-requests?status=PENDING|APPROVED|REJECTED&limit=50 - Danh sách nghỉ phép
- PATCH /api/admin/leave-requests/:id - Duyệt/từ chối nghỉ phép
- GET /api/admin/attendance-settings - Cài đặt giờ làm việc
- PUT /api/admin/attendance-settings - Cập nhật cài đặt giờ làm
- GET /api/admin/attendance-settings/logs?limit=50 - Lịch sử thay đổi
- GET /api/admin/schedules/daily?weekStart=YYYY-MM-DD - Lịch theo ngày trong tuần
- POST /api/admin/schedules/daily - Tạo/cập nhật lịch ngày
- DELETE /api/admin/schedules/daily/:id - Xóa lịch ngày override
- GET /api/admin/schedules/logs?employeeId=...&limit=50 - Lịch sử thay đổi lịch (admin)
- GET /api/admin/payroll-formula-settings - Cấu hình công thức lương
- PUT /api/admin/payroll-formula-settings - Cập nhật công thức lương
- GET /api/admin/reward-rules - Quy tắc thưởng
- PUT /api/admin/reward-rules - Cập nhật quy tắc thưởng
- GET /api/admin/rewards?month=MM&year=YYYY&employeeId=...&status=PENDING|APPROVED|CANCELLED&type=MONEY|MATERIAL - Danh sách thưởng
- POST /api/admin/rewards - Tạo thưởng
- PATCH /api/admin/rewards/:id - Duyệt/hủy thưởng
- POST /api/admin/rewards/auto-calculate?month=MM&year=YYYY - Tự động tính thưởng
- GET /api/admin/rewards/auto-calculate/employees?month=MM&year=YYYY - Danh sách đủ điều kiện thưởng
- GET /api/admin/reward-records?month=MM&year=YYYY - Lịch sử thưởng tự động
- GET /api/admin/disciplines?month=MM&year=YYYY&employeeId=...&type=LATE|ABSENT|VIOLATION|FORGOTTEN_CHECKOUT|OTHER&status=RECORDED|APPROVED|WAIVED - Danh sách kỷ luật
- POST /api/admin/disciplines - Ghi nhận kỷ luật
- PATCH /api/admin/disciplines/:id - Duyệt/bỏ qua kỷ luật
- GET /api/admin/departments - Danh sách phòng ban
- POST /api/admin/departments - Tạo phòng ban
- PUT /api/admin/departments/:id - Cập nhật phòng ban
- DELETE /api/admin/departments/:id - Xóa phòng ban
- GET /api/admin/departments/:id/employees - Nhân viên theo phòng ban
- PATCH /api/admin/departments/:id/employees - Gán/bỏ gán nhân viên
- GET /api/admin/positions - Danh sách chức vụ
- POST /api/admin/positions - Tạo chức vụ
- PUT /api/admin/positions/:id - Cập nhật chức vụ
- DELETE /api/admin/positions/:id - Xóa chức vụ
- GET /api/admin/positions/:id/employees - Nhân viên theo chức vụ
- GET /api/admin/departments/:deptId/positions - Chức vụ theo phòng ban

---

## 11) Realtime Notifications (Socket.IO)

- Socket.IO auth bằng JWT (token trên client)
- Room theo user: user:<userId>
- Event: new_notification

---

## 12) MongoDB collections

- users, employees
- attendance, attendanceAdjustments, leaveRequests
- schedules, employeeDailySchedules, scheduleChangeLogs
- payrolls, payroll_audits, payrollFormulaSettingsLogs
- notifications
- departments, positions
- rewards, rewardRules, employeeRewardRecords, disciplines

---

## 13) Scripts (Backend)

- npm run create-admin: tạo admin
- npm run update-ip: cập nhật EXPO_PUBLIC_API_URL theo IP LAN
- npm run assign-employee-qrcodes: gán QR code cho nhân viên
- npm run seed-payrolls: seed dữ liệu payroll (test)

---

## 14) Hình ảnh demo

### Tổng quan hệ thống
![system-overview](./Images_readme/01-system-overview.png)

### Mobile app
![02-mobile-login](./Images_readme/02-mobile-login.png)
![03-mobile-home](./Images_readme/03-mobile-home.png)
![04-mobile-checkin](./Images_readme/04-mobile-checkin.png)
![05-mobile-checkout](./Images_readme/05-mobile-checkout.png)
![06-attendance-ui](./Images_readme/06-Attendance%20UI.png)
![06-mobile-leave-request](./Images_readme/06-mobile-leave-request.png)
![07-mobile-notification](./Images_readme/07-mobile-notification.png)
![08-mobile-payslip](./Images_readme/08-mobile-payslip.png)

### Admin web
![09-admin-login](./Images_readme/09-admin-login.png)
![11-admin-employees](./Images_readme/11-admin-employees.png)
![11-admin-employees-2](./Images_readme/11-admin-employees_2.png)
![11-admin-employees-v3](./Images_readme/11-admin-employees_v3.png)
![12-admin-attendance](./Images_readme/12-admin-attendance.png)
![13-admin-leave-approval](./Images_readme/13-admin-leave-approval.png)
![14-admin-payroll-bulk](./Images_readme/14-admin-payroll-bulk.png)
![14-admin-payroll-bulk-2](./Images_readme/14-admin-payroll-bulk_2.png)
![15-admin-payroll-detail](./Images_readme/15-admin-payroll-detail.png)
![16-admin-reports](./Images_readme/16-admin-reports.png)
![17-admin-settings-1](./Images_readme/17-admin-settings_1.png)
![17-admin-settings-2](./Images_readme/17-admin-settings_2.png)

---

## 15) Ghi chú

- API base URL ở mobile: EXPO_PUBLIC_API_URL trong Frontend/.env
- API base URL ở admin: VITE_API_URL trong adminSide/.env
- Swagger UI: /api-docs để xem schema và test nhanh
