# n8n Workflow Setup Guide

## Quick Setup Instructions

### 1. Import the Workflow
1. Open your n8n instance
2. Click **"+ New"** → **"Import from File"**
3. Upload the `n8n-workflow.json` file
4. Click **"Import"**

### 2. Required Credentials Setup

#### Google Drive OAuth2 API
1. Go to **Settings** → **Credentials** → **Create Credential**
2. Select **"Google Drive OAuth2 API"**
3. Enter your Google OAuth2 credentials:
   - **Client ID**: From Google Cloud Console
   - **Client Secret**: From Google Cloud Console
4. Save as: `google-drive-oauth`

#### Google Sheets OAuth2 API
1. Create another credential: **"Google Sheets OAuth2 API"**
2. Use the same Google OAuth2 credentials
3. Save as: `google-sheets-oauth`

#### HubSpot API
1. Create credential: **"HubSpot API"**
2. Enter your HubSpot API key
3. Save as: `hubspot-api`

#### SMTP Email
1. Create credential: **"SMTP"**
2. Configure your email server settings:
   - **Host**: Your SMTP server
   - **Port**: Usually 587 or 465
   - **Username**: Your email
   - **Password**: Your email password
3. Save as: `smtp-credentials`

### 3. Configure Workflow Parameters

#### Google Drive Trigger Node
- **Folder to Watch**: Replace `GOOGLE_DRIVE_FOLDER_ID` with your actual folder ID
- **File Formats**: Already configured for CSV, XLSX, XLS

#### Email Notification Nodes
- **From Email**: Replace `noreply@yourdomain.com` with your sender email
- **To Email**: Replace `admin@yourdomain.com` with recipient email

### 4. Test the Workflow

#### Test Data Upload
1. Upload a sample CSV file to your monitored Google Drive folder
2. Check the workflow execution in n8n
3. Verify data appears in Google Sheets
4. Confirm contacts are created in HubSpot
5. Check for email notifications

## Workflow Node Details

### 🔄 Node Flow Overview
```
Google Drive Trigger → Download CSV → Parse CSV → Create Google Sheet → 
Upload Raw Data → Data Cleaning → Apply Filtering → Business Logic → 
HubSpot Upload → Generate Report → Send Email
```

### 📊 Data Processing Steps

#### Step 1: Google Drive Monitoring
- **Trigger**: File creation/modification
- **Formats**: CSV, XLSX, XLS
- **Size Limit**: 50MB per file

#### Step 2: Google Sheets Upload
- **Operation**: Create new spreadsheet
- **Sheet Name**: "Raw Data"
- **Auto-naming**: "Cleaned_Data_YYYY-MM-DD_HH-MM"

#### Step 3: Data Cleaning Process
**Automated Cleaning:**
- Email validation (RFC 5322 compliant)
- Phone formatting: (XXX) XXX-XXXX
- Name standardization (Title Case)
- Address standardization
- Completeness scoring (0-100%)

#### Step 4: Data Filtering
**Geographic Filter:**
- States: NY, NJ, CT, PA (configurable)
- Completeness: ≥70% threshold
- Email: Must be present

#### Step 5: Business Logic Application
**Quality Enhancement:**
- Vehicle age filtering (≤20 years old)
- Registration date filtering (≤2 years old)
- Quality scoring with bonuses/penalties
- Lead segmentation (High/Medium/Low Quality)

#### Step 6: HubSpot Integration
**Contact Creation:**
- Batch size: 100 contacts
- Field mapping: Email, Name, Phone, Address
- Custom properties: Lead source, segment, quality score

#### Step 7: Report Generation
**Processing Statistics:**
- Total records processed
- Success rates and quality metrics
- Segmentation breakdown
- Geographic distribution
- Data quality insights
- Processing duration

## 🔧 Customization Options

### Filtering Criteria (Easily Modifiable)
```javascript
// In "Apply Filtering Criteria" node
geographic_filter: "NY,NJ,CT,PA"  // Add/remove states
completeness_threshold: 70        // Adjust percentage
quality_threshold: 60            // Minimum quality score
```

### Business Rules (Code Node)
```javascript
// In "Apply Business Logic" node
vehicle_age_limit: 20           // Years
registration_age_limit: 2       // Years
quality_bonus_complete: 10      // Points for complete data
quality_penalty_fake: -20       // Points for suspicious data
```

### HubSpot Field Mapping
```javascript
// In "Upload to HubSpot" node - Add custom fields:
{
  "name": "custom_field_name",
  "value": "={{ $json.source_field }}"
}
```

## 🚨 Error Handling

### Automatic Error Management
- **Try/Catch Logic**: Built into workflow
- **Error Notifications**: Automatic email alerts
- **Retry Logic**: HubSpot API calls retry on failure
- **Graceful Degradation**: Continue processing on non-critical errors

### Manual Error Resolution
1. Check n8n execution log
2. Review error email notifications  
3. Fix data source issues
4. Re-run workflow manually if needed

## 📈 Monitoring & Optimization

### Performance Monitoring
- **Processing Speed**: Target <1 minute per 1,000 records
- **Success Rate**: Target ≥95% successful executions
- **Quality Score**: Target ≥70% average quality

### Optimization Tips
1. **Large Files**: Consider splitting files >10,000 records
2. **API Limits**: Monitor HubSpot API usage
3. **Google Quotas**: Track Drive/Sheets API quotas
4. **Processing Time**: Optimize during off-peak hours

## 🔄 Workflow Maintenance

### Regular Updates
- **Monthly**: Review filtering criteria effectiveness
- **Quarterly**: Update quality scoring algorithms
- **As Needed**: Add new data sources or HubSpot fields

### Backup and Version Control
- **Export Workflow**: Regular JSON exports
- **Test Environment**: Duplicate for testing changes
- **Version Notes**: Document workflow modifications

## 📞 Support and Troubleshooting

### Common Issues
1. **File Not Detected**: Check Google Drive permissions
2. **Cleaning Failures**: Verify data format consistency
3. **HubSpot Errors**: Check API key and rate limits
4. **Email Issues**: Verify SMTP configuration

### Performance Troubleshooting
- **Slow Processing**: Reduce batch sizes
- **Memory Issues**: Split large files
- **API Timeouts**: Add retry delays

---

**Ready to Process Data!** 🚀

Once configured, your workflow will automatically:
✅ Monitor Google Drive for new CSV files  
✅ Clean and validate data quality  
✅ Apply business filtering criteria  
✅ Upload qualified contacts to HubSpot  
✅ Generate comprehensive reports  
✅ Send email notifications 