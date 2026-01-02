<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <title>T31 役男簽到系統 (2026)</title>
    <!-- 引入 Excel 處理函式庫 -->
    <script src="cdn.sheetjs.com"></script>
    <style>
        body { font-family: "Microsoft JhengHei", sans-serif; background: #eef2f3; display: flex; justify-content: center; padding: 20px; }
        .card { background: white; padding: 30px; border-radius: 15px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); width: 100%; max-width: 400px; }
        h2 { text-align: center; color: #2c3e50; margin-bottom: 25px; }
        .form-group { margin-bottom: 20px; }
        label { display: block; margin-bottom: 8px; font-weight: bold; color: #34495e; }
        select { width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 6px; font-size: 16px; background-color: #fafafa; }
        button { width: 100%; padding: 12px; border: none; border-radius: 6px; font-size: 16px; cursor: pointer; transition: 0.3s; }
        .btn-submit { background: #3498db; color: white; margin-bottom: 10px; }
        .btn-submit:hover { background: #2980b9; }
        .btn-export { background: #27ae60; color: white; }
        .btn-export:hover { background: #219150; }
    </style>
</head>
<body>

<div class="card">
    <h2>T31 役男簽到表 (2026)</h2>
    <form id="signForm">
        <div class="form-group">
            <label>役男姓名</label>
            <select id="name" required>
                <option value="">請選擇姓名...</option>
                <option value="王小明">王小明</option>
                <option value="李曉華">李曉華</option>
                <option value="張大同">張大同</option>
            </select>
        </div>

        <div class="form-group">
            <label>簽到/簽退</label>
            <select id="status" required>
                <option value="簽到">簽到</option>
                <option value="簽退">簽退</option>
            </select>
        </div>

        <div class="form-group">
            <label>假別狀態</label>
            <select id="leaveType" required>
                <option value="正常出勤">正常出勤</option>
                <option value="事假">事假</option>
                <option value="病假">病假</option>
                <option value="公假">公假</option>
                <option value="罰勤">罰勤</option>
            </select>
        </div>

        <button type="submit" class="btn-submit">提交資料並儲存</button>
    </form>
    <button onclick="exportToExcel()" class="btn-export">下載彙整 Excel 報表</button>
</div>

<script>
    // 1. 請在此處貼上您的 Google Apps Script URL
    const GOOGLE_SCRIPT_URL = '您的_APPS_SCRIPT_網址';
    
    // 用於儲存當前工作階段的多筆紀錄
    let sessionRecords = [];

    document.getElementById('signForm').addEventListener('submit', function(e) {
        e.preventDefault();

        const formData = {
            name: document.getElementById('name').value,
            status: document.getElementById('status').value,
            leaveType: document.getElementById('leaveType').value,
            timestamp: new Date().toLocaleString('zh-TW', { hour12: false })
        };

        // A. 儲存至免費雲端資料庫 (Google Sheets)
        fetch(GOOGLE_SCRIPT_URL, {
            method: 'POST',
            mode: 'no-cors',
            body: JSON.stringify(formData)
        }).then(() => {
            alert('單筆資料已成功同步至雲端資料庫！');
            
            // B. 彙整至本地清單供匯出使用
            sessionRecords.push(formData);
            this.reset();
        }).catch(err => alert('提交失敗: ' + err));
    });

    // C. 彙整多筆資料並自動儲存 Excel 到桌面（下載資料夾）
    function exportToExcel() {
        if (sessionRecords.length === 0) {
            alert('尚無可匯出的資料紀錄！');
            return;
        }

        // 轉換格式為 Excel 工作表
        const ws = XLSX.utils.json_to_sheet(sessionRecords);
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "2026簽到紀錄匯總");

        // 執行下載動作
        const dateStr = new Date().toISOString().slice(0, 10);
        XLSX.writeFile(wb, `T31_Sign_Report_2026_${dateStr}.xlsx`);
    }
</script>

</body>
</html>
