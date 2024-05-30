---
layout: post
title: Import Excel
categories: Java-8
---

### Import excel 


#### Add dependency:

```java
        <dependency>
            <groupId>org.apache.poi</groupId>
            <artifactId>poi-ooxml</artifactId>
            <version>4.0.0</version>
        </dependency>
```
#### folder helper:

- **ReadExcelUtils**

```java

import com.amazonaws.util.StringUtils;
import lombok.extern.slf4j.Slf4j;
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.ss.usermodel.DateUtil;
import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;

import java.text.DateFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.*;

@Slf4j
public class ReadExcelUtils {
    public static List<Map<String, String>> dataFile(Iterator<Row> iteratorRow, List<String> mapHeader) {
        List<Map<String, String>> listDataFile = new ArrayList();
        iteratorRow.next();
        DataFormatter dataFormatter = new DataFormatter();

        while (iteratorRow.hasNext()) {
            Row currentRow = iteratorRow.next();
            Iterator<Cell> currentCell = currentRow.iterator();
            Map<String, String> mapCellData = new HashMap<>();
            while (currentCell.hasNext()) {
                try {
                    Cell cell = currentCell.next();
                    String value;
                    if (cell.getCellType() == CellType.NUMERIC && DateUtil.isCellDateFormatted(cell)) {
                        value = new SimpleDateFormat("dd/MM/yyyy").format(cell.getDateCellValue());
                    } else {
                        value = dataFormatter.formatCellValue(cell);
                    }
                    mapCellData.put(mapHeader.get(cell.getColumnIndex()), value);
                } catch (IndexOutOfBoundsException e) {
                    log.warn(e.getMessage());
                    break;
                }
            }
            if (mapCellData.size() != 0)
                listDataFile.add(mapCellData);
        }
        return listDataFile;
    }

    public static List<String> mapHeaderFile(XSSFSheet sheet, XSSFWorkbook workbook) {
        try {
            List<String> mapHeader = new ArrayList<>();
            sheet = workbook.getSheetAt(0);
            Row row = sheet.getRow(0);
            Iterator<Cell> cellIteratorHeader = row.iterator();

            while (cellIteratorHeader.hasNext()) {
                try {
                    Cell currentCell = cellIteratorHeader.next();
                    mapHeader.add(currentCell.getStringCellValue());
                } catch (ArrayIndexOutOfBoundsException e) {
                    log.warn(e.getMessage());
                    break;
                }
            }
            return mapHeader;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
    public static Boolean validateMapHeader(List<String> mapHeader, String[] headerSkill) {
        if (mapHeader.size() != headerSkill.length) {
            return false;
        }
        List<String> headerSkills = List.of(headerSkill);
        for (int i = 0; i < mapHeader.size(); i++) {
            Object item1 = mapHeader.get(i);
            Object item2 = headerSkills.get(i);
            if (!item1.equals(item2)) {
                return false; // Nếu có ít nhất một cặp giá trị không giống nhau, danh sách không giống nhau
            }
        }
        return true;

    }

    public static String getCellValue(String data) {
        if (StringUtils.isNullOrEmpty(data))
            return "";
        return data.trim();
    }

    public static String getFileHeaderByIndex(String[] headers, int index) {
        return headers[index].trim();
    }

    public static Date convertDate(String date) {
        if (StringUtils.isNullOrEmpty(date))
            return null;
        SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd");

        try {
            Date newDate = new SimpleDateFormat("dd/MM/yyyy").parse(date);
            return newDate;
        } catch (ParseException e) {
        }

        return null;
    }

    public static String convertDateFormat(String inputDate) {
        DateFormat originalFormat = new SimpleDateFormat("MM/dd/yyyy");
        DateFormat targetFormat = new SimpleDateFormat("yyyy-MM-dd");
        try {
            Date date = originalFormat.parse(inputDate);
            return targetFormat.format(date);
        } catch (ParseException e) {
            e.printStackTrace();
            return null;
        }
    }
}

```

- **ReadFileExcelHoliday**

```java

import com.amazonaws.util.StringUtils;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.collections4.CollectionUtils;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.springframework.beans.BeanUtils;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Component;

import java.io.InputStream;
import java.text.ParseException;
import java.util.*;
import java.util.stream.Collectors;

@Component
@RequiredArgsConstructor
@Slf4j
public class ReadFileExcelHoliday extends AbstractService {

    private final ResponseUtil responseUtil;
    private final HolidayRepository holidayRepository;
    private final HolidayService holidayService;
    private static String SHEET = "data";
    private static String[] HEADERS = {"Holiday Name", "Holiday Date"};

    /**
     * The function ValidateListStaffInfo, used to check input data from Excel
     *
     * @param inputStream
     */
    public ResponseEntity<Response> importFileExcelListHoliday(InputStream inputStream) {

        try {
            XSSFWorkbook workbook = new XSSFWorkbook(inputStream);
            XSSFSheet sheet = workbook.getSheet(SHEET);
            if (sheet == null) {
                return responseUtil.errorResponse(ReturnCode.ERROR, "Sheet does not exist");
            }
            Iterator<Row> iteratorRow = sheet.iterator();
            List<String> mapHeader = mapHeaderFile(sheet, workbook);
            Boolean checkHeader = validateMapHeader(mapHeader, HEADERS);
            if (!checkHeader) {
                return responseUtil.errorResponse(ERROR, "Header file error.");
            }
            List<Map<String, String>> listDataFile = dataFile(iteratorRow, mapHeader);

            // Parsing file content into list of object
            List<HolidayExcelDto> parsedObjects = this.parsedObjects(listDataFile);

            // check file is blank
            if (CollectionUtils.isEmpty(parsedObjects)) {
                return responseUtil.errorResponse(ERROR, "File Excel import is blank");
            }

            // remove object null data
            for (HolidayExcelDto holidayExcelDto : new ArrayList<>(parsedObjects)) {
                if (holidayExcelDto.isEmpty(holidayExcelDto)) {
                    parsedObjects.remove(holidayExcelDto);
                }
            }

            // Filter valid data
            List<HolidayExcelDto> listValidData = this.filterValidData(parsedObjects);
            // Import file excel
            if (listValidData.isEmpty()) {
                return createListHoliday(parsedObjects);
            }
            log.info("after validate staff info: number of header from file = {}, " +
                    "number of header from server = {}, " +
                    "invalid row = {}", listValidData.size(), parsedObjects.size(), listValidData.size() - parsedObjects.size());

            final int total = parsedObjects.size();
            final int inValidRows = listValidData.size();
            final int validRows = total - inValidRows;

            final UploadDto<HolidayExcelDto> uploadDto = new UploadDto<>();
            uploadDto.setTotalRows(total);
            uploadDto.setValidRows(validRows);
            uploadDto.setInvalidRows(inValidRows);
            uploadDto.setDetailRecordDtos(listValidData);

            return responseUtil.response(ERROR, uploadDto, "Error import file Excel");
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    private List<HolidayExcelDto> parsedObjects(List<Map<String, String>> listDataFile) {
        String[] header = HEADERS;
        return listDataFile
                .stream()
                .map(data -> {
                    HolidayExcelDto holidayExcelDto = new HolidayExcelDto();
                    holidayExcelDto.setHolidayName(getCellValue(data.get(getFileHeaderByIndex(header, 0))));
                    holidayExcelDto.setHolidayDate(getCellValue(data.get(getFileHeaderByIndex(header, 1))));
                    return holidayExcelDto;
                }).collect(Collectors.toList());
    }

    private ResponseEntity<Response> createListHoliday(List<HolidayExcelDto> parsedObjects) {

        List<CreateHolidayRequest> mappingData = mappingDataHoliday(parsedObjects);
        List<Object> createdHolidayIds = new ArrayList<>();
        boolean rollbackNeeded = false;

        // write log rollback
        try {
            for (CreateHolidayRequest createHolidayRequest : mappingData) {
                ResponseEntity<Response> createHoliday = holidayService.create(createHolidayRequest);
                if (!SUCCESS.equals(createHoliday.getBody().getStatus())) {
                    rollbackNeeded = true;
                    break;
                } else {
                    // Save object holiday
                    createdHolidayIds.add(createHoliday.getBody().getData());
                }
            }
        } catch (Exception e) {
            // write log
            log.error("Error occurred during holiday creation: " + e.getMessage(), e);
            rollbackNeeded = true;
        }

        // action rollback
        if (rollbackNeeded) {
            for (Object holidayId : createdHolidayIds) {
                Holiday holidayModel = new Holiday();
                BeanUtils.copyProperties(holidayId, holidayModel);

                holidayRepository.delete(holidayModel);
            }

            return responseUtil.response(ERROR, "", "Error import file Excel.");
        } else {
            return responseUtil.response(SUCCESS, "", "Import file success");
        }
    }

    private List<CreateHolidayRequest> mappingDataHoliday(List<HolidayExcelDto> parsedObjects) {
        List<CreateHolidayRequest> mappingHolidayImportResults = new ArrayList<>();
        for (HolidayExcelDto holidayExcelDto : parsedObjects) {
            mappingHolidayImportResults.add(mappingHoliday(holidayExcelDto));
        }
        return mappingHolidayImportResults;
    }

    private CreateHolidayRequest mappingHoliday(HolidayExcelDto holidayExcelDto) {

        CreateHolidayRequest createHolidayRequest = new CreateHolidayRequest();

        String holidayName = holidayExcelDto.getHolidayName();
        String holidayDate = holidayExcelDto.getHolidayDate();

        createHolidayRequest.setDate(convertDateFormat(holidayDate));
        createHolidayRequest.setName(holidayName);
        createHolidayRequest.setNationwide(true);
        createHolidayRequest.setRepeatType(HolidayRepeatType.NONE);
        return createHolidayRequest;
    }

    private List<HolidayExcelDto> filterValidData(List<HolidayExcelDto> parsedObjects) throws ParseException {
        List<HolidayExcelDto> validHolidayImportResults = new ArrayList<>();
        int index = 2;
        for (HolidayExcelDto holidayExcelDto : parsedObjects) {
            validateHoliday(holidayExcelDto, index);
            if (!holidayExcelDto.isValid()) {
                validHolidayImportResults.add(holidayExcelDto);
            }
            index++;
        }
        return validHolidayImportResults;
    }

    private HolidayExcelDto validateHoliday(HolidayExcelDto holidayExcelDto, Integer index) throws ParseException {

        String holidayName = holidayExcelDto.getHolidayName();
        String holidayDate = holidayExcelDto.getHolidayDate();

        List<String> errorList = new ArrayList<>();
        if (StringUtils.isNullOrEmpty(holidayName)) {
            errorList.add(getMessage("holiday.name.empty"));
        }
        if (StringUtils.isNullOrEmpty(holidayDate)) {
            errorList.add(getMessage("holiday.date.empty"));
        } else {
            Holiday holiday = holidayRepository.findByDate(DateUtil.stringToDate(convertDateFormat(holidayDate), DateUtil.FORMAT_YYYY_MM_DD, true));
            if (Objects.nonNull(holiday)) {
                errorList.add(getMessage("holiday.date.duplicate"));
            }

        }
        holidayExcelDto.setNumericalOrder(index);
        holidayExcelDto.setErrorList(errorList);

        return holidayExcelDto;
    }

}

```

- **Enity holiday**

```java
import lombok.Data;

import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.Date;

@Data
@Entity
@Table(name = "holiday")
public class Holiday extends GenericEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id", columnDefinition = "BIGINT UNSIGNED")
    private Long id;

    @Column()
    String name;

    @Column(unique = true)
    @Temporal(TemporalType.DATE)
    Date date;

    @Column()
    String repeatType;

    @Column(name = "nationwide", columnDefinition = "BOOLEAN DEFAULT TRUE")
    boolean nationwide;

    @Column(
            name = "deleted_at",
            nullable = true
    )
    private LocalDateTime deletedAt;

    public Holiday() {

    }
}

```

- **Service holiday**

```java
public interface HolidayService {
    ResponseEntity<Response> getList(GetHolidayRequest getHolidayRequest) throws ParseException;

    ResponseEntity<Response> create(CreateHolidayRequest holidayRequest) throws ParseException;

    ResponseEntity<Response> update(Long id, UpdateHolidayRequest holidayRequest) throws ParseException;

    ResponseEntity<Response> delete(Long id);

    ResponseEntity<Response> details(Long id);
}
```


- **HolidayServiceImpl**

```java
import org.apache.commons.lang3.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.springframework.data.jpa.domain.Specification;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;

import javax.validation.Valid;
import java.text.ParseException;
import java.util.*;

@Service
@Transactional(propagation = Propagation.REQUIRED)
public class HolidayServiceImpl extends AbstractService implements HolidayService {
    @Autowired
    private HolidayRepository holidayRepository;

    @Autowired
    private HolidayMapper holidayMapper;

    @Override
    public ResponseEntity<Response> getList(GetHolidayRequest getHolidayRequest) throws ParseException {
        if (getHolidayRequest.getPage() > 0) {
            getHolidayRequest.setPage(getHolidayRequest.getPage() - 1);
        }

        String[] mustMatchSortFields = {"date", DefaultPageOptions.DEFAULT_SORT_FIELD};
        if (Arrays.stream(mustMatchSortFields).noneMatch(getHolidayRequest.getSortField()::equalsIgnoreCase)) {
            getHolidayRequest.setSortField(DefaultPageOptions.DEFAULT_SORT_FIELD);
        }

        Sort.Direction[] directions = {Sort.Direction.ASC, Sort.Direction.DESC};
        if (Arrays.stream(directions).noneMatch(getHolidayRequest.getSortDirection()::equals)) {
            getHolidayRequest.setSortDirection(Sort.Direction.DESC);
        }

        Pageable pageRequest = org.springframework.data.domain.PageRequest.of(getHolidayRequest.getPage(),
                getHolidayRequest.getSize(), Sort.by(getHolidayRequest.getSortDirection(), getHolidayRequest.getSortField()));
        var conditions = new ArrayList<Specification<Holiday>>();

        conditions.add(HolidaySpecifications.isNotDeleted(true));

        if (StringUtils.isNotBlank(getHolidayRequest.getStart())) {
            conditions.add(HolidaySpecifications.fromDate(DateUtil.stringToDate(getHolidayRequest.getStart(), DateUtil.FORMAT_YYYY_MM_DD, true)));
        }

        if (StringUtils.isNotBlank(getHolidayRequest.getEnd())) {
            conditions.add(HolidaySpecifications.toDate(DateUtil.stringToDate(getHolidayRequest.getEnd(), DateUtil.FORMAT_YYYY_MM_DD, true)));
        }

        if (StringUtils.isNotBlank(getHolidayRequest.getSearchField())) {
            conditions.add(HolidaySpecifications.likeName(getHolidayRequest.getSearchField()));
        }

        Specification<Holiday> specs = SpecificationUtil.buildANDSpecification(conditions);

        Page<Holiday> holidayPage = holidayRepository.findAll(specs, pageRequest);

        PagedListModel pagedListModel = PagedListBuilder.buildModel(holidayPage, holidayPage.getContent());

        return responseUtil.successResponse(pagedListModel);
    }

    private ValidateModel validateBeforeCreate(@Valid CreateHolidayRequest holidayRequest) throws ParseException {
        if (StringUtils.isBlank(holidayRequest.getDate())) {
            return ValidateModel.builder()
                    .returnCode(ReturnCode.INVALID_REQUEST)
                    .message(getMessage("holiday.date.empty"))
                    .build();
        }

        try {
            Holiday holiday = holidayRepository.findByDate(DateUtil.stringToDate(holidayRequest.getDate(), DateUtil.FORMAT_YYYY_MM_DD, true));

            if (holiday != null) {
                return ValidateModel.builder()
                        .returnCode(ReturnCode.CONFLICT_DATA)
                        .message(getMessage("holiday.date.duplicate"))
                        .build();
            }

        } catch (Exception e) {
            return ValidateModel.builder()
                    .returnCode(ReturnCode.INVALID_REQUEST)
                    .message(getMessage("holiday.date.invalid"))
                    .build();
        }

        return ValidateModel.builder()
                .returnCode(ReturnCode.SUCCESS)
                .message(getMessage("common.success"))
                .build();
    }

    @Override
    public ResponseEntity<Response> create(CreateHolidayRequest holidayRequest) throws ParseException {
        ValidateModel validateResponse = validateBeforeCreate(holidayRequest);

        if (!validateResponse.getReturnCode().equals(ReturnCode.SUCCESS)) {
            return responseUtil.errorResponse(validateResponse.getReturnCode(), validateResponse.getMessage());
        }

        HolidayModel holidayModel = HolidayModel.builder()
                .date(
                        DateUtil.stringToDate(holidayRequest.getDate(), DateUtil.FORMAT_YYYY_MM_DD, true)
                )
                .name(holidayRequest.getName())
                .repeatType(holidayRequest.getRepeatType())
                .nationwide(holidayRequest.isNationwide())
                .build();

        Holiday holiday = holidayMapper.toHoliday(holidayModel);

        holiday = holidayRepository.save(holiday);

        holidayModel = holidayMapper.toHolidayModel(holiday);

        return responseUtil.successResponse(holidayModel);
    }

    private ValidateModel validateBeforeUpdate(UpdateHolidayRequest holidayRequest, Long id) {
        HolidayModel holidayModel = holidayMapper.toHolidayModel(holidayRepository.findByIdAndDeletedAtIsNull(id));

        if (Objects.isNull(holidayModel)) {
            return ValidateModel.builder()
                    .returnCode(ReturnCode.DATA_NOT_FOUND)
                    .message(getMessage("holiday.error.notFound"))
                    .build();
        }

        return ValidateModel.builder()
                .returnCode(ReturnCode.SUCCESS)
                .message(getMessage("common.success"))
                .build();
    }

    @Override
    public ResponseEntity<Response> update(Long id, UpdateHolidayRequest holidayRequest) throws ParseException {
        ValidateModel validateResponse = validateBeforeUpdate(holidayRequest, id);

        if (!validateResponse.getReturnCode().equals(ReturnCode.SUCCESS)) {
            return responseUtil.errorResponse(validateResponse.getReturnCode(), validateResponse.getMessage());
        }



        Holiday holiday = holidayRepository.findByIdAndDeletedAtIsNull(id);

        holiday.setDate(
                DateUtil.stringToDate(holidayRequest.getDate(), DateUtil.FORMAT_YYYY_MM_DD, true)
        );

        holiday.setName(holidayRequest.getName());
        holiday.setRepeatType(holidayRequest.getRepeatType().getName());
        holiday.setNationwide(holidayRequest.isNationwide());

        return responseUtil.successResponse(holidayMapper.toHolidayModel(holiday));
    }

    @Override
    public ResponseEntity<Response> delete(Long id) {
        Holiday holiday = holidayRepository.findByIdAndDeletedAtIsNull(id);

        if (Objects.isNull(holiday)) {
            return responseUtil.errorResponse(ReturnCode.DATA_NOT_FOUND, getMessage("holiday.error.notFound"));
        }

        holiday.setDeletedAt(DateUtil.travelTime());
        return responseUtil.successResponse(holidayMapper.toHolidayModel(holiday));
    }

    @Override
    public ResponseEntity<Response> details(Long id) {
        Holiday holiday = holidayRepository.findByIdAndDeletedAtIsNull(id);

        if (Objects.isNull(holiday)) {
            return responseUtil.errorResponse(ReturnCode.DATA_NOT_FOUND, getMessage("holiday.error.notFound"));
        }

        return responseUtil.successResponse(holidayMapper.toHolidayModel(holiday));
    }
}

```

- **HolidayExcelDto**

```java
@Getter
@Setter
public class HolidayExcelDto extends ImportDTO {
    @JsonIgnore
    private String holidayName;
    @JsonIgnore
    private String holidayDate;

    public static boolean isEmpty(HolidayExcelDto holidayExcelDto) {
        return StringUtils.isNullOrEmpty(holidayExcelDto.getHolidayName())
                && StringUtils.isNullOrEmpty(holidayExcelDto.getHolidayDate());
    }
}

```

- **ImportDTO**

```java
@Getter
@Setter
@JsonInclude(value = JsonInclude.Include.NON_NULL)
public class ImportDTO {

    private int numericalOrder;
    private List<String> errorList;
    @JsonIgnore
    public boolean isValid() {
        return errorList == null || errorList.size() == 0;
    }
}

```


- Update rollback

```java


import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.collections4.CollectionUtils;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.springframework.beans.BeanUtils;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Component;

import java.io.InputStream;
import java.text.ParseException;
import java.util.*;
import java.util.stream.Collectors;

@Component
@RequiredArgsConstructor
@Slf4j
public class ReadFileExcelHoliday extends AbstractService {

    private final ResponseUtil responseUtil;
    private final HolidayRepository holidayRepository;
    private final HolidayService holidayService;
    private static String SHEET = "data";
    private static String[] HEADERS = {"Holiday Name", "Holiday Date"};

    /**
     * The function ValidateListStaffInfo, used to check input data from Excel
     *
     * @param inputStream
     */
    public ResponseEntity<Response> importFileExcelListHoliday(InputStream inputStream) {

        try {
            XSSFWorkbook workbook = new XSSFWorkbook(inputStream);
            XSSFSheet sheet = workbook.getSheet(SHEET);
            if (sheet == null) {
                return responseUtil.errorResponse(ReturnCode.ERROR, "Sheet does not exist");
            }
            Iterator<Row> iteratorRow = sheet.iterator();
            List<String> mapHeader = mapHeaderFile(sheet, workbook);
            Boolean checkHeader = validateMapHeader(mapHeader, HEADERS);
            if (!checkHeader) {
                return responseUtil.errorResponse(ERROR, "Header file error.");
            }
            List<Map<String, String>> listDataFile = dataFile(iteratorRow, mapHeader);

            // Parsing file content into list of object
            List<HolidayExcelDto> parsedObjects = this.parsedObjects(listDataFile);

            // check file is blank
            if (CollectionUtils.isEmpty(parsedObjects)) {
                return responseUtil.errorResponse(ERROR, "File Excel import is blank");
            }

            // remove object null data
            for (HolidayExcelDto holidayExcelDto : new ArrayList<>(parsedObjects)) {
                if (holidayExcelDto.isEmpty(holidayExcelDto)) {
                    parsedObjects.remove(holidayExcelDto);
                }
            }

            // Filter valid data
            List<HolidayExcelDto> listValidData = this.filterValidData(parsedObjects);
            // Import file excel
            if (listValidData.isEmpty()) {
                return createListHoliday(parsedObjects);
            }
            log.info("after validate staff info: number of header from file = {}, " +
                    "number of header from server = {}, " +
                    "invalid row = {}", listValidData.size(), parsedObjects.size(), listValidData.size() - parsedObjects.size());

            final int total = parsedObjects.size();
            final int inValidRows = listValidData.size();
            final int validRows = total - inValidRows;

            final UploadDto<HolidayExcelDto> uploadDto = new UploadDto<>();
            uploadDto.setTotalRows(total);
            uploadDto.setValidRows(validRows);
            uploadDto.setInvalidRows(inValidRows);
            uploadDto.setDetailRecordDtos(listValidData);

            return responseUtil.response(ERROR, uploadDto, "Error import file Excel");
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    private List<HolidayExcelDto> parsedObjects(List<Map<String, String>> listDataFile) {
        String[] header = HEADERS;
        return listDataFile
                .stream()
                .map(data -> {
                    HolidayExcelDto holidayExcelDto = new HolidayExcelDto();
                    holidayExcelDto.setHolidayName(getCellValue(data.get(getFileHeaderByIndex(header, 0))));
                    holidayExcelDto.setHolidayDate(getCellValue(data.get(getFileHeaderByIndex(header, 1))));
                    return holidayExcelDto;
                }).collect(Collectors.toList());
    }

    private ResponseEntity<Response> createListHoliday(List<HolidayExcelDto> parsedObjects) {

        List<CreateHolidayRequest> mappingData = mappingDataHoliday(parsedObjects);
        List<Object> createdHolidayIds = new ArrayList<>();
        boolean rollbackNeeded = false;
        List<String> errorList = new ArrayList<>();
        String errorMessage = "";
        Integer indexError = 1;

        // write log rollback
        try {
            for (CreateHolidayRequest createHolidayRequest : mappingData) {
                ResponseEntity<Response> createHoliday = holidayService.create(createHolidayRequest);
                indexError++;
                if (!SUCCESS.equals(createHoliday.getBody().getStatus())) {
                    errorMessage = createHoliday.getBody().getMessage();
                    errorList.add(errorMessage);
                    rollbackNeeded = true;
                    break;
                } else {
                    // Save object holiday
                    createdHolidayIds.add(createHoliday.getBody().getData());
                }
            }
        } catch (Exception e) {
            // write log
            log.error("Error occurred during holiday creation: " + e.getMessage(), e);
            rollbackNeeded = true;
        }

        // action rollback
        if (rollbackNeeded) {
            for (Object holidayId : createdHolidayIds) {
                Holiday holidayModel = new Holiday();
                BeanUtils.copyProperties(holidayId, holidayModel);

                holidayRepository.delete(holidayModel);
            }
            HolidayExcelDto holidayExcelDto = new HolidayExcelDto();
            holidayExcelDto.setNumericalOrder(indexError);
            holidayExcelDto.setErrorList(errorList);

            List<HolidayExcelDto> holidayExcelDtoList = new ArrayList<>();
            holidayExcelDtoList.add(holidayExcelDto);

            UploadDto<HolidayExcelDto> uploadDto = new UploadDto<>();
            uploadDto.setDetailRecordDtos(holidayExcelDtoList);

            String error = "Error import file Excel: " + errorMessage + " Row: " + indexError;
            return responseUtil.response(ERROR, uploadDto, error);
        } else {
            return responseUtil.response(SUCCESS, "", "Import file success");
        }
    }

    private List<CreateHolidayRequest> mappingDataHoliday(List<HolidayExcelDto> parsedObjects) {
        List<CreateHolidayRequest> mappingHolidayImportResults = new ArrayList<>();
        for (HolidayExcelDto holidayExcelDto : parsedObjects) {
            mappingHolidayImportResults.add(mappingHoliday(holidayExcelDto));
        }
        return mappingHolidayImportResults;
    }

    private CreateHolidayRequest mappingHoliday(HolidayExcelDto holidayExcelDto) {

        CreateHolidayRequest createHolidayRequest = new CreateHolidayRequest();

        String holidayName = holidayExcelDto.getHolidayName();
        String holidayDate = holidayExcelDto.getHolidayDate();

        createHolidayRequest.setDate(convertDateFormat(holidayDate));
        createHolidayRequest.setName(holidayName);
        createHolidayRequest.setNationwide(true);
        createHolidayRequest.setRepeatType(HolidayRepeatType.NONE);
        return createHolidayRequest;
    }

    private List<HolidayExcelDto> filterValidData(List<HolidayExcelDto> parsedObjects) throws ParseException {
        List<HolidayExcelDto> validHolidayImportResults = new ArrayList<>();
        int index = 2;
        for (HolidayExcelDto holidayExcelDto : parsedObjects) {
            validateHoliday(holidayExcelDto, index);
            if (!holidayExcelDto.isValid()) {
                validHolidayImportResults.add(holidayExcelDto);
            }
            index++;
        }
        return validHolidayImportResults;
    }

    private HolidayExcelDto validateHoliday(HolidayExcelDto holidayExcelDto, Integer index) throws ParseException {

        String holidayName = holidayExcelDto.getHolidayName();
        String holidayDate = holidayExcelDto.getHolidayDate();

        List<String> errorList = new ArrayList<>();
        if (StringUtils.isNullOrEmpty(holidayName)) {
            errorList.add(getMessage("holiday.name.empty"));
        }
        if (StringUtils.isNullOrEmpty(holidayDate)) {
            errorList.add(getMessage("holiday.date.empty"));
        } else {
            Holiday holiday = holidayRepository.findByDate(DateUtil.stringToDate(convertDateFormat(holidayDate), DateUtil.FORMAT_YYYY_MM_DD, true));
            if (Objects.nonNull(holiday)) {
                errorList.add(getMessage("holiday.date.duplicate"));
            }

        }
        holidayExcelDto.setNumericalOrder(index);
        holidayExcelDto.setErrorList(errorList);

        return holidayExcelDto;
    }

}

```