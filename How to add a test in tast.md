## How to add a test to an existing package
**Note**:  test to an existing package without modifying the main.go file, just add it directly
### For example, add display component sanity check in platform package
~/chromiumos/src/platform/tast-tests/src/chromiumos/tast/local/bundles/cros/platform/display_component_sanity_check.go
```
// Copyright 2019 The Chromium OS Authors. All rights reserved.
// Use of this source code is governed by a BSD-style license that can be
// found in the LICENSE file.

// Add a cpu topology verify test on crostini.

package platform

import (
        "context"
        "os/exec"
        "time"
        "fmt"
        "strconv"
        "strings"

        "chromiumos/tast/testing"
)

func init() {
        testing.AddTest(&testing.Test{
                Func:     DisplayComponentSanityCheck,
                Desc:     "check display components sanity",
                Contacts: []string{"xiaoling.song@intel.com", "zhenzhenx.ren@intel.com"},
                Attr:     []string{"group:mainline"},
                Params: []testing.Param{
                        // Parameters generated by toolkit_test.go. DO NOT EDIT.
                        {
                                Timeout:   30 * time.Minute,
                        }},
        })
}

func DisplayComponentSanityCheck(ctx context.Context, s *testing.State) {

        testfiles := []string{
                "/sys/kernel/debug/dri/0/gt/uc/guc_info",
                "/sys/kernel/debug/dri/0/gt/uc/huc_info",
                "/sys/kernel/debug/dri/0/i915_dmc_info",
                "/sys/kernel/debug/dri/0/i915_dmc_info",
                "/sys/kernel/debug/dri/0/i915_edp_psr_status",
        }

        for _, testfile := range testfiles {
                out, err := exec.Command("bash", "-c", fmt.Sprintf("cat %s | wc -l", testfile)).CombinedOutput();
                if err != nil {
                        s.Errorf("failed %v", err)
                }
                fileSize, _ := strconv.ParseInt(strings.TrimSpace(string(out)), 10, 32)
                if fileSize == 0 {
                        s.Errorf("the %v file is null", testfile)
                }

        }
}
```
```
tast -verbose run $ip platform.DisplayComponentSanityCheck
```
## How to add test case to a newly created package
### For example, Create chromedemo package and add date_format.go test case
**Note**: 新创建的package，必须现在main.go中通过import package先注册，main.go中下划线导入的包通过 init 函数注册相应package的测试。
~/chromiumos/src/platform/tast-tests/src/chromiumos/tast/local/bundles/cros/chromedemo/date_format.go
```
// Copyright 2019 The Chromium OS Authors. All rights reserved.
// Use of this source code is governed by a BSD-style license that can be
// found in the LICENSE file.

package chromedemo

import (
        "context"
        "strings"

        "chromiumos/tast/common/testexec"
        "chromiumos/tast/shutil"
        "chromiumos/tast/testing"
)

func init() {
        testing.AddTest(&testing.Test{
                Func: DateFormat,
                Desc: "Checks that the date command prints dates as expected",
                Contacts: []string{
                        "me@chromium.org",         // Test author
                        "tast-users@chromium.org", // Backup mailing list
                },
                Attr: []string{"group:mainline", "informational"},
        })
}

func DateFormat(ctx context.Context, s *testing.State) {
        for _, tc := range []struct {
                date string // value to pass via --date flag
                spec string // spec to pass in "+"-prefixed arg
                exp  string // expected UTC output (minus trailing newline)
        }{
                {"2004-02-29 16:21:42 +0100", "%Y-%m-%d %H:%M:%S", "2004-02-29 15:21:42"},
                {"Sun, 29 Feb 2004 16:21:42 -0800", "%Y-%m-%d %H:%M:%S", "2004-03-01 00:21:42"},
        } {
                cmd := testexec.CommandContext(ctx, "date", "--utc", "--date="+tc.date, "+"+tc.spec)
                if out, err := cmd.Output(testexec.DumpLogOnError); err != nil {
                        s.Errorf("%q failed: %v", shutil.EscapeSlice(cmd.Args), err)
                } else if outs := strings.TrimRight(string(out), "\n"); outs != tc.exp {
                        s.Errorf("%q printed %q; want %q", shutil.EscapeSlice(cmd.Args), outs, tc.exp)
                }
        }
}
```
```
在~/chromiumos/src/platform/tast-tests/src/chromiumos/tast/local/bundles/cros/main.go import中添加如下行
_ "chromiumos/tast/local/bundles/cros/chromedemo"
```
```
tast -verbose run $ip chromedemo.DateFormat
```
