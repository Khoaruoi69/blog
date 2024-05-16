---
layout: post
title: CRUD example
categories: Java-8
---

### Example CRUD, Search

#### **Enity**

**CustomerMaster**

```java
@Getter
@Setter
@Entity
@Table(name = "customer_master")
public class CustomerMaster extends GenericEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id", columnDefinition = "BIGINT UNSIGNED")
    private Long id;
    @Column(unique = true, nullable = false)
    private String customerCode;
    @Column(unique = true, nullable = false)
    private String customerName;
    private String customerType;
    private String customerEmail;
    private String addressPrimary;
    private String addressSecondary;
    private String country;
    private String zipCode;
    private String phoneNumberPrimary;
    private String phoneNumberSecondary;
    private String contactPersonFullName;
    private String contactPersonPhone;
    private String contactPersonPosition;
    private String currency;
    @Column(nullable = false)
    private Integer state = SateConstant.ACTIVE;
    private LocalDateTime deletedAt;
    private String updateBy;
    @OneToMany(mappedBy = "customerMaster", fetch = FetchType.LAZY)
    private List<ProjectInfo> projectInfo;
}
```

**ProjectInfo**

```java
@Data
@Entity
@Table(name = "project_info")
public class ProjectInfo extends GenericEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id", columnDefinition = "BIGINT UNSIGNED")
    private Long id;

    @Column(name = "project_no", unique = true, nullable = false)
    private String projectNo;

    @Column(name = "project_name", nullable = false)
    private String projectName;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "customer_master", referencedColumnName = "id")
    private CustomerMaster customerMaster;
}
```

#### DTO

**CustomerMasterDto**

```java
@Getter
@Setter
public class CustomerMasterDto {
    private String customerCode;
    private String customerName;
    private String customerType;
    private String customerEmail;
    private String addressPrimary;
    private String addressSecondary;
    private String country;
    private String zipCode;
    private String phoneNumberPrimary;
    private String phoneNumberSecondary;
    private String contactPersonFullName;
    private String contactPersonPhone;
    private String contactPersonPosition;
    private String currency;
    private List<Long> listProjectId;
}
```

**CreateCustomerMasterRequest**

```java
@Getter
@Setter
public class CreateCustomerMasterRequest {
    private String customerCode;
    private String customerName;
    private String customerType;
    private String customerEmail;
    private String addressPrimary;
    private String addressSecondary;
    private String country;
    private String zipCode;
    private String phoneNumberPrimary;
    private String phoneNumberSecondary;
    private String contactPersonFullName;
    private String contactPersonPhone;
    private String contactPersonPosition;
    private String currency;
    private List<Long> listProjectId;

    /**
     * The fromCreateCustomerMaster function is used to transform CustomerMaster into CreateCustomerMasterRequest
     *
     * @param
     * @return CustomerMaster
     */
    public static CustomerMaster fromCreateCustomerMaster(CreateCustomerMasterRequest createCustomerMasterRequest) {
        CustomerMaster customerMaster = new CustomerMaster();
        BeanUtils.copyProperties(createCustomerMasterRequest, customerMaster);

        return customerMaster;
    }

}
```

**SearchCustomerMasterRequest**

```java
@Getter
@Setter
public class SearchCustomerMasterRequest extends PageOptionsRequest {
    private String customerCode;
    private String customerName;
    private Integer state;
}
```

**UpdateCustomerMasterRequest**

```java
@Getter
@Setter
public class UpdateCustomerMasterRequest {
    private String customerCode;
    private String customerName;
    private String customerType;
    private String customerEmail;
    private String addressPrimary;
    private String addressSecondary;
    private String country;
    private String zipCode;
    private String phoneNumberPrimary;
    private String phoneNumberSecondary;
    private String contactPersonFullName;
    private String contactPersonPhone;
    private String contactPersonPosition;
    private String currency;
    private List<Long> listProjectId;
    /**
     * The fromCreateCustomerMaster function is used to transform CustomerMaster into CreateCustomerMasterRequest
     *
     * @param
     * @return CustomerMaster
     */
    public static CustomerMaster fromCreateCustomerMaster(UpdateCustomerMasterRequest createCustomerMasterRequest) {
        CustomerMaster customerMaster = new CustomerMaster();
        BeanUtils.copyProperties(createCustomerMasterRequest, customerMaster);
        return customerMaster;
    }
}
```

### Repository

```java
public interface CustomerMasterRepository extends CrudRepository<CustomerMaster, Long>, JpaSpecificationExecutor<CustomerMaster> {
    Optional<CustomerMaster> findByCustomerName(String customerName);

    Optional<CustomerMaster> findByCustomerCode(String customerName);
}
```

### Service


**CustomerMasterService**

```java
public interface CustomerMasterService {
    ResponseEntity<Response>  createCustomerMaster(CreateCustomerMasterRequest createCustomerMasterRequest);
    ResponseEntity<Response> updateCustomer(Long id, UpdateCustomerMasterRequest customerMasterRequest);
    ResponseEntity<Response> deleteCustomer(Long id);
    ResponseEntity<Response> searchCustomer(SearchCustomerMasterRequest searchCustomerMasterRequest);

}
```

**CustomerMasterImpl**

```java
@Service
@RequiredArgsConstructor
@Transactional(propagation = Propagation.REQUIRED)
public class CustomerMasterImpl extends AbstractService implements CustomerMasterService {
    private final CustomerMasterRepository customerMasterRepository;
    private final ProjectInfoRepository projectInfoRepository;

    @Override
    public ResponseEntity<Response> createCustomerMaster(CreateCustomerMasterRequest createCustomerMasterRequest) {
        try {
            ResponseEntity<Response> validate = this.validateCreateCustomerMaster(createCustomerMasterRequest);
            if (Objects.nonNull(validate)) {
                return validate;
            }
            UserModel userModel = getUserInfo().getUser();
            String userName = userModel.getFirstName().concat(" " + userModel.getLastName());
            CustomerMaster customerMaster = CreateCustomerMasterRequest.fromCreateCustomerMaster(createCustomerMasterRequest);
            customerMaster.setUpdateBy(userName);
            CustomerMaster saveCustomerMaster = customerMasterRepository.save(customerMaster);
            List<Long> listProjectId = createCustomerMasterRequest.getListProjectId();
            if (!listProjectId.isEmpty()) {
                Iterable<ProjectInfo> projectInfos = projectInfoRepository.findAllById(listProjectId);
                for (ProjectInfo projectInfo : projectInfos) {
                    projectInfo.setCustomerMaster(saveCustomerMaster);
                }
                projectInfoRepository.saveAll(projectInfos);
            }
            return responseUtil.successResponse(getMessage("customer.success.create"));
        } catch (Exception exception) {
            throw new RuntimeException(exception.getMessage());
        }
    }

    @Override
    public ResponseEntity<Response> updateCustomer(Long id, UpdateCustomerMasterRequest updateCustomerMasterRequest) {
        try {
            ResponseEntity<Response> validate = this.validateUpdateCustomerMaster(id, updateCustomerMasterRequest);
            if (Objects.nonNull(validate)) {
                return validate;
            }
            UserModel userModel = getUserInfo().getUser();
            String userName = userModel.getFirstName().concat(" " + userModel.getLastName());
            Optional<CustomerMaster> customerMaster = customerMasterRepository.findById(id);
            CustomerMaster existingCustomer = customerMaster.get();
            existingCustomer.setUpdateBy(userName);
            BeanUtils.copyProperties(updateCustomerMasterRequest, existingCustomer);
            List<Long> listProjectId = updateCustomerMasterRequest.getListProjectId();
            if (!listProjectId.isEmpty()) {
                Iterable<ProjectInfo> projectInfos = projectInfoRepository.findAllById(listProjectId);
                for (ProjectInfo projectInfo : projectInfos) {
                    projectInfo.setCustomerMaster(existingCustomer);
                }
                projectInfoRepository.saveAll(projectInfos);
            }
            customerMasterRepository.save(existingCustomer);
            BeanUtils.copyProperties(existingCustomer, updateCustomerMasterRequest);
            return responseUtil.response(ReturnCode.SUCCESS, updateCustomerMasterRequest, getMessage("customer.success.update"));
        } catch (Exception exception) {
            throw new RuntimeException(exception.getMessage());
        }
    }

    @Override
    public ResponseEntity<Response> deleteCustomer(Long id) {
        try {
            Optional<CustomerMaster> customerMasterOptional = customerMasterRepository.findById(id);
            if (customerMasterOptional.isEmpty()) {
                return responseUtil.errorResponse(ReturnCode.ERROR, getMessage("customer.error.notFound"));
            }
            UserModel userModel = getUserInfo().getUser();
            String userName = userModel.getFirstName().concat(" " + userModel.getLastName());
            customerMasterOptional.get().setUpdateBy(userName);
            customerMasterOptional.get().setState(INACTIVE);
            customerMasterOptional.get().setDeletedAt(LocalDateTime.now());
            customerMasterRepository.save(customerMasterOptional.get());
            return responseUtil.successResponse(getMessage("customer.success.delete"));
        } catch (Exception ex) {
            throw new RuntimeException(ex.getMessage());
        }
    }

    @Override
    public ResponseEntity<Response> searchCustomer(SearchCustomerMasterRequest searchRequest) {
        try {
            // trim request
            List<Specification<CustomerMaster>> conditions = new ArrayList<Specification<CustomerMaster>>();
            this.validateAndMappingSearchRequest(searchRequest);

            Pageable pageRequest = org.springframework.data.domain.PageRequest.of(searchRequest.getPage(),
                    searchRequest.getSize(), Sort.by(searchRequest.getSortDirection(), searchRequest.getSortField()));

            if (!StringUtils.isNullOrEmpty(searchRequest.getCustomerName())) {
                conditions.add(CustomerMasterSpecifications.hasCustomerName(searchRequest.getCustomerName()));
            }
            if (!StringUtils.isNullOrEmpty(searchRequest.getCustomerCode())) {
                conditions.add(CustomerMasterSpecifications.hasCustomerCode(searchRequest.getCustomerCode()));
            }
            if (Objects.nonNull(searchRequest.getState())) {
                conditions.add(CustomerMasterSpecifications.hasCustomerState(searchRequest.getState()));
            }
            Specification<CustomerMaster> specs = SpecificationUtil.buildANDSpecification(conditions);
            Page<CustomerMaster> customerMasters = customerMasterRepository.findAll(specs, pageRequest);
            List<CustomerMasterDto> customerMasterDtoList = convertCustomerSearch(customerMasters);
            PagedListModel pagedListModel = new PagedListModel<>();
            if (Objects.nonNull(customerMasters)) {
                pagedListModel = PagedListBuilder.buildModel(customerMasters, customerMasterDtoList);
            }
            return responseUtil.successResponse(pagedListModel);
        } catch (Exception ex) {
            log.error(ex.getMessage());
            return responseUtil.errorResponse(ReturnCode.ERROR_RESPONSE, getMessage("common.invalidRequest"));
        }
    }

    private List<CustomerMasterDto> convertCustomerSearch(Page<CustomerMaster> customerMasters) {
        if (!customerMasters.getContent().isEmpty()) {
            List<CustomerMasterDto> customerMasterDtos = new ArrayList<>();
            for (CustomerMaster customerMaster : customerMasters.getContent()) {
                CustomerMasterDto customerMasterDto = new CustomerMasterDto();
                BeanUtils.copyProperties(customerMaster, customerMasterDto);
                customerMasterDtos.add(customerMasterDto);
            }
            return customerMasterDtos;
        }
        return null;
    }

    private void validateAndMappingSearchRequest(SearchCustomerMasterRequest searchRequest) {
        if (searchRequest.getPage() > 0) {
            searchRequest.setPage(searchRequest.getPage() - 1);
        }
        if (!StringUtils.isNullOrEmpty(searchRequest.getCustomerName())) {
            searchRequest.setCustomerName(searchRequest.getCustomerName().trim());
        }
        if (!StringUtils.isNullOrEmpty(searchRequest.getCustomerCode())) {
            searchRequest.setCustomerCode(searchRequest.getCustomerCode().trim());
        }

        String[] mustMatchSortFields = {"customerName", DefaultPageOptions.DEFAULT_SORT_FIELD};
        // default customer state ACTIVE
        searchRequest.setState(ACTIVE);
        if (!StringUtils.isNullOrEmpty(searchRequest.getSortField())) {
            if (Arrays.stream(mustMatchSortFields).noneMatch(searchRequest.getSortField()::equalsIgnoreCase)) {
                searchRequest.setSortField(DefaultPageOptions.DEFAULT_SORT_FIELD);
            }

            Sort.Direction[] directions = {Sort.Direction.ASC, Sort.Direction.DESC};
            if (Arrays.stream(directions).noneMatch(searchRequest.getSortDirection()::equals)) {
                searchRequest.setSortDirection(Sort.Direction.DESC);
            }
        } else {
            searchRequest.setSortField(DefaultPageOptions.DEFAULT_SORT_FIELD);
            searchRequest.setSortDirection(Sort.Direction.DESC);
        }
    }


    private ResponseEntity<Response> validateCreateCustomerMaster(CreateCustomerMasterRequest createCustomerMasterRequest) {
        String customerName = createCustomerMasterRequest.getCustomerName();
        String customerCode = createCustomerMasterRequest.getCustomerCode();
        String customerEmail = createCustomerMasterRequest.getCustomerEmail();
        List<Long> listProjectId = createCustomerMasterRequest.getListProjectId();
        Optional<CustomerMaster> customerMasterName = customerMasterRepository.findByCustomerName(customerName);
        Optional<CustomerMaster> customerMasterCode = customerMasterRepository.findByCustomerCode(customerCode);
        if (StringUtils.isNullOrEmpty(customerCode)) {
            return responseUtil.errorResponse(ReturnCode.ERROR, getMessage("customer.code.notBlank"));
        }
        if (StringUtils.isNullOrEmpty(customerName)) {
            return responseUtil.errorResponse(ReturnCode.ERROR, getMessage("customer.name.notBlank"));

        }
        if (!StringUtils.isNullOrEmpty(customerEmail)) {

            if (!CommonMethod.validateEmail(customerEmail)) {
                return responseUtil.errorResponse(ReturnCode.ERROR, getMessage("customer.email.invalid"));
            }
        }
        if (customerMasterName.isPresent()) {
            return responseUtil.errorResponse(ReturnCode.ERROR, getMessage("customer.name.exists"));
        }
        if (customerMasterCode.isPresent()) {
            return responseUtil.errorResponse(ReturnCode.ERROR, getMessage("customer.code.exists"));
        }
        if (!listProjectId.isEmpty()) {
            for (Long id : listProjectId) {
                Optional<ProjectInfo> projectInfo = projectInfoRepository.findById(id);
                if (projectInfo.isEmpty()) {
                    return responseUtil.errorResponse(ReturnCode.ERROR, getMessage("project.error.notFound"));
                }
            }
        }
        return null;
    }

    private ResponseEntity<Response> validateUpdateCustomerMaster(Long id, UpdateCustomerMasterRequest updateCustomerMasterRequest) {
        String customerName = updateCustomerMasterRequest.getCustomerName();
        String customerCode = updateCustomerMasterRequest.getCustomerCode();
        String customerEmail = updateCustomerMasterRequest.getCustomerEmail();
        List<Long> listProjectId = updateCustomerMasterRequest.getListProjectId();

        Optional<CustomerMaster> customerMasterOptional = customerMasterRepository.findById(id);
        if (customerMasterOptional.isEmpty()) {
            return responseUtil.errorResponse(ReturnCode.ERROR, getMessage("customer.error.notFound"));
        }

        CustomerMaster existingCustomerMaster = customerMasterOptional.get();

        if (!StringUtils.isNullOrEmpty(customerName) && !customerName.equals(existingCustomerMaster.getCustomerName())) {
            if (customerMasterRepository.findByCustomerName(customerName).isPresent()) {
                return responseUtil.errorResponse(ReturnCode.ERROR, getMessage("customer.name.exists"));
            }
        }

        if (!StringUtils.isNullOrEmpty(customerCode) && !customerCode.equals(existingCustomerMaster.getCustomerCode())) {
            if (customerMasterRepository.findByCustomerCode(customerCode).isPresent()) {
                return responseUtil.errorResponse(ReturnCode.ERROR, getMessage("customer.code.exists"));
            }
        }

        if (!StringUtils.isNullOrEmpty(customerEmail) && !CommonMethod.validateEmail(customerEmail)) {
            return responseUtil.errorResponse(ReturnCode.ERROR, getMessage("customer.email.invalid"));
        }
        if (!listProjectId.isEmpty()) {
            for (Long projectId : listProjectId) {
                Optional<ProjectInfo> projectInfo = projectInfoRepository.findById(projectId);
                if (projectInfo.isEmpty()) {
                    return responseUtil.errorResponse(ReturnCode.ERROR, getMessage("project.error.notFound"));
                }
            }
        }

        return null;
    }
}

```

#### Specitification

**CustomerMasterSpecifications**

``` java
public class CustomerMasterSpecifications {
    public static Specification<CustomerMaster> hasCustomerName(String customerName) {
        return (root, query, cb) -> cb.like(cb.upper(root.get("customerName")), '%' + customerName.toUpperCase(Locale.ROOT) + '%');
    }

    public static Specification<CustomerMaster> hasCustomerCode(String customerCode) {
        return (root, query, cb) -> cb.like(cb.upper(root.get("customerCode")), '%' + customerCode.toUpperCase(Locale.ROOT) + '%');
    }

    public static Specification<CustomerMaster> hasCustomerState(Integer state) {
        return (root, query, cb) -> cb.equal(cb.upper(root.get("state")), state);
    }

}
```


### Controller

```java
@RestController
@RequiredArgsConstructor
public class CustomerMasterController {
    private final CustomerMasterService customerMasterService;

    @PreAuthorize("hasAnyAuthority(@roleBod, @roleSupperAdmin)")
    @RequestMapping(value = RestURIConstant.ADMIN_CREATE_CUSTOMER_MASTER, method = RequestMethod.POST)
    public ResponseEntity<?> createCustomerMaster(@RequestBody CreateCustomerMasterRequest createCustomerMasterRequestDto) {
        return customerMasterService.createCustomerMaster(createCustomerMasterRequestDto);
    }

    @PreAuthorize("hasAnyAuthority(@roleBod, @roleSupperAdmin)")
    @RequestMapping(value = RestURIConstant.ADMIN_UPDATE_CUSTOMER_MASTER, method = RequestMethod.PUT)
    public ResponseEntity<?> updateCustomerMaster(@PathVariable Long id, @RequestBody UpdateCustomerMasterRequest updateCustomerMasterRequest) {
        return customerMasterService.updateCustomer(id, updateCustomerMasterRequest);
    }

    @PreAuthorize("hasAnyAuthority(@roleBod, @roleSupperAdmin)")
    @RequestMapping(value = RestURIConstant.ADMIN_DELETE_CUSTOMER_MASTER, method = RequestMethod.PATCH)
    public ResponseEntity<?> deleteCustomerMaster(@PathVariable Long id) {
        return customerMasterService.deleteCustomer(id);
    }

    @RequestMapping(value = RestURIConstant.SEARCH_CUSTOMER_MASTER, method = RequestMethod.POST)
    public ResponseEntity<?> searchCustomerMaster(@RequestBody SearchCustomerMasterRequest searchCustomerMasterRequestDto) {
        return customerMasterService.searchCustomer(searchCustomerMasterRequestDto);
    }
}
```